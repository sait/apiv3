# Articulos

Rutas de Artículos

| Acción                                | Ruta                              |
|---------------------------------------|-----------------------------------|
| [Crear articulo](#crear-articulo)     | POST /api/v3/articulos            |
| [Leer por clave](#leer-por-clave)     | GET  /api/v3/articulos/:numart    |
| [Actualizar](#actualizar-articulo)    | PUT  /api/v3/articulos/:numart    |
| [Listar articulos](#buscar-artículos) | GET  /api/v3/articulos?filters... |
| [Subir imagenes](#subir-imagenes-s3obj) | POST /api/v3/articulos/:numart/imagenes |
| [Consultar imagenes](#consultar-imagenes) | GET /api/v3/articulos/:numart/imagenes |
| [Reordenar imagenes](#reordenar-imagenes) | PUT /api/v3/articulos/:numart/imagenes/order |
| [Borrar imagen](#borrar-imagen) | DELETE /api/v3/articulos/:numart/imagenes/:uid |
| [Borrar varias imagenes](#borrar-varias-imagenes) | DELETE /api/v3/articulos/:numart/imagenes |

Campos de Artículo

| Campo      | tipo     | Significado                                            |
|------------|----------|--------------------------------------------------------|
| id         | int      | id incremental disponible solo através de SAIT API     |
| created    | datetime | timestamp del momento de creación                      |
| updated    | datetime | timestamp ultima actualizacion                         |
| numart     | varchar  | clave del articulo (Obligatorio)                       |
| desc       | varchar  | descripción del articulo (Obligatorio)                 |
| obs        | text     | descripción larga u observaciones del artículo         |
| linea      | varchar  | clave de linea para clasificacion                      |
| familia    | varchar  | clave de familia para clasificacion                    |
| categoria  | varchar  | clave de categoria para clasifiacion                   |
| activo     | tinyint  | boleano para indicar si articulo está activo           |
| marca      | varchar  |                                                        |
| modelo     | varchar  |                                                        |
| unidad     | varchar  | unidad de venta: PZ,CJ,MTS (Obligatorio)               |
| precio1    | decimal  | precio segun lista 1 sin impuestos                     |
| precio2    | decimal  | precio segun lista 2 sin impuestos                     |
| precio3    | decimal  | precio segun lista 3 sin impuestos                     |
| precio4    | decimal  | precio segun lista 4 sin impuestos                     |
| precio5    | decimal  | precio segun lista 5 sin impuestos                     |
| preciopub  | decimal  | precio publico con impuestos incluidos                 |
| divisa     | char     | moneda para precio P=Pesos D=Dolar                     |
| ultcosto   | decimal  | ultimo costo de compra                                 |
| clavesat   | varchar  | clave segun SAT (Obligatorio)                          |
| excento    | tinyint  | boleando indicando si es excento de impuestos para SAT |
| impuesto1  | decimal  | % de IVA segun SAT                                     |
| impuesto2  | decimal  | % de IEPS segun SAT                                    |
| esmatpelig | tinyint  | Es Material Peligroso segun SAT                        |
| matpelig   | varchar  | Clave SAT para Material Peligroso                      |
| existencia | decimal  | Existencia TOTAL sumando TODAS las sucursales          |
| imagenes   | []string | URLs `lg` desde s3obj para compatibilidad legacy |

---
### Imagenes de articulos con s3obj

Las imagenes nuevas de articulos se manejan desde `inventario/articulos` usando `pkg/s3obj`.

La ruta vigente para subir imagenes de articulos es:

```txt
POST /api/v3/articulos/:numart/imagenes
```

La ruta global legacy `POST /api/v3/imagenes` ya no se usa para articulos. Tampoco se actualizan `arts.foto` ni `arts.fotos` al subir imagenes nuevas; la relacion queda en `objects` de `s3obj`.

Los eventos legacy `ADDFOTO` ya no alimentan imagenes de articulos. Durante la migracion a `s3obj`, esos eventos se omiten con log.

El tipo de objeto usado internamente es:

```go
ObjTypeArticuloImagen // 99
```

El cliente no envia `objTypeID`. Las rutas de articulos usan ese tipo de objeto de forma interna y usan `numart` como `pk` de negocio en `s3obj`.

Las consultas normales de articulos regresan dos campos:

- `imagenes`: arreglo legacy de strings. Cada string usa la URL `lg` para mantener compatibilidad con pantallas existentes.
- `images`: estructura nueva con `uuid`, `sm`, `md` y `lg`.

Si el articulo ya tiene imagenes `uploaded` en `s3obj`, ambos campos se llenan desde `s3obj`. Si no hay imagenes `uploaded`, ambos regresan vacios. La migracion legacy se encarga de copiar `arts.foto` y `arts.fotos` al nuevo bucket.

Ejemplo de respuesta parcial de `GET /api/v3/articulos/:numart`:

```json
{
  "numart": "ART-001",
  "desc": "Articulo demo",
  "imagenes": [
    "https://pub.saitcdn.com/777/items-image/4355e7f6-1a44-456d-a209-834a5a0ec59f_lg.webp",
    "https://pub.saitcdn.com/777/items-image/84374f52-24d7-4775-a182-dbe4d17647d2_lg.webp"
  ],
  "images": [
    {
      "uuid": "4355e7f6-1a44-456d-a209-834a5a0ec59f",
      "sm": "https://pub.saitcdn.com/777/items-image/4355e7f6-1a44-456d-a209-834a5a0ec59f_sm.webp",
      "md": "https://pub.saitcdn.com/777/items-image/4355e7f6-1a44-456d-a209-834a5a0ec59f_md.webp",
      "lg": "https://pub.saitcdn.com/777/items-image/4355e7f6-1a44-456d-a209-834a5a0ec59f_lg.webp"
    },
    {
      "uuid": "84374f52-24d7-4775-a182-dbe4d17647d2",
      "sm": "https://pub.saitcdn.com/777/items-image/84374f52-24d7-4775-a182-dbe4d17647d2_sm.webp",
      "md": "https://pub.saitcdn.com/777/items-image/84374f52-24d7-4775-a182-dbe4d17647d2_md.webp",
      "lg": "https://pub.saitcdn.com/777/items-image/84374f52-24d7-4775-a182-dbe4d17647d2_lg.webp"
    }
  ]
}
```

`filename` se guarda internamente en `objects.filename` para auditoria y usos futuros, pero no se expone en la respuesta publica normal del articulo.

Las variantes `sm`, `md` y `lg` viven en `object_types` y representan ancho en pixeles. Para `items-image`, los valores actuales son `sm=100`, `md=400` y `lg=1200`.

La migracion de imagenes legacy del bucket anterior al nuevo flujo se ejecuta con el comando `migrar-imagenes-articulos-s3obj`. No se borran imagenes legacy durante la migracion.

Ejemplo de respuesta completa del envoltorio HTTP con campos principales:

```json
{
  "result": {
    "id": 244,
    "numart": "                TRRE",
    "desc": "TRIPITAS DE RES",
    "foto": "trre.jpg",
    "fotos": "trre2.jpg\n",
    "imagenes": [
      "https://pub.saitcdn.com/777/items-image/81f739ff-83ea-4af7-a988-34166bad61f0_lg.webp",
      "https://pub.saitcdn.com/777/items-image/113ffe23-d67a-4a1c-8925-233e5c489dfb_lg.webp"
    ],
    "images": [
      {
        "uuid": "81f739ff-83ea-4af7-a988-34166bad61f0",
        "sm": "https://pub.saitcdn.com/777/items-image/81f739ff-83ea-4af7-a988-34166bad61f0_sm.webp",
        "md": "https://pub.saitcdn.com/777/items-image/81f739ff-83ea-4af7-a988-34166bad61f0_md.webp",
        "lg": "https://pub.saitcdn.com/777/items-image/81f739ff-83ea-4af7-a988-34166bad61f0_lg.webp"
      },
      {
        "uuid": "113ffe23-d67a-4a1c-8925-233e5c489dfb",
        "sm": "https://pub.saitcdn.com/777/items-image/113ffe23-d67a-4a1c-8925-233e5c489dfb_sm.webp",
        "md": "https://pub.saitcdn.com/777/items-image/113ffe23-d67a-4a1c-8925-233e5c489dfb_md.webp",
        "lg": "https://pub.saitcdn.com/777/items-image/113ffe23-d67a-4a1c-8925-233e5c489dfb_lg.webp"
      }
    ],
    "unidades": null
  },
  "error": ""
}
```

Pruebas rapidas desde el repo:

```bash
cd apiv3/src
go test ./inventario/articulos
```

Si el entorno local tiene problemas con el cache global de Go, se puede usar un cache temporal:

```bash
cd apiv3/src
GOCACHE=/tmp/codex-go-build go test -mod=mod ./inventario/articulos
```

Las pruebas unitarias actuales validan la conversion de objetos `s3obj` a `imagenes`, la compatibilidad de entrada con strings legacy y la respuesta enfocada de imagenes con estados. Las pruebas que suben archivos reales, reordenan contra DB o borran en S3 deben tratarse como pruebas de integracion.

---
### Subir imagenes s3obj

POST /api/v3/articulos/:numart/imagenes

Request:

```json
{
  "imagenes": [
    {
      "filename": "principal.jpg",
      "contentBase64": "base64-de-la-imagen"
    },
    {
      "filename": "detalle.png",
      "contentBase64": "base64-de-la-imagen"
    }
  ]
}
```

Cada item requiere `contentBase64`. `filename` es opcional y se usa para que el cliente pueda relacionar la respuesta con su archivo local.

La ruta llama internamente:

```go
s3obj.StoreImage(
	ObjTypeArticuloImagen, // objTypeID = 99
	numart,                // pk
	contentBase64,         // contentBase64
	"",                    // uid
)
```

Respuesta:

```json
{
  "result": [
    {
      "filename": "principal.jpg",
      "uid": "uid-1",
      "status": "pending"
    },
    {
      "filename": "detalle.png",
      "error": "imagen_invalida"
    }
  ]
}
```

`pending` significa que la imagen ya quedo registrada y el upload/procesamiento puede terminar en background.

---
### Consultar imagenes

GET /api/v3/articulos/:numart/imagenes

Esta ruta regresa unicamente las imagenes del articulo, sin los demas datos del articulo. Incluye `uid`, `status` y `sort_order` para poder borrar, mostrar pendientes y reordenar.

Respuesta:

```json
{
  "result": [
    {
      "uid": "uid-1",
      "status": "uploaded",
      "url": "https://pub.saitcdn.com/777/items-image/uid-1_lg.webp",
      "variantes": {
        "sm": "https://pub.saitcdn.com/777/items-image/uid-1_sm.webp",
        "md": "https://pub.saitcdn.com/777/items-image/uid-1_md.webp",
        "lg": "https://pub.saitcdn.com/777/items-image/uid-1_lg.webp"
      },
      "sort_order": 1
    },
    {
      "uid": "uid-2",
      "status": "pending",
      "sort_order": 2
    }
  ]
}
```

---
### Reordenar imagenes

PUT /api/v3/articulos/:numart/imagenes/order

Request:

```json
{
  "orderedUIDs": ["uid-3", "uid-1", "uid-2"]
}
```

La lista debe incluir todos los UIDs actuales del articulo. `s3obj.Order` valida lista incompleta, UIDs duplicados y UIDs que no pertenezcan al articulo.

Respuesta:

```json
{
  "result": {
    "ok": true
  }
}
```

---
### Borrar imagen

DELETE /api/v3/articulos/:numart/imagenes/:uid

La ruta valida que el UID pertenezca al articulo y al tipo `ObjTypeArticuloImagen` antes de llamar a `s3obj.Delete`.

Respuesta:

```json
{
  "result": {
    "ok": true
  }
}
```

---
### Borrar varias imagenes

DELETE /api/v3/articulos/:numart/imagenes

Request:

```json
{
  "uids": ["uid-1", "uid-2", "uid-3"]
}
```

La ruta valida primero que todos los UIDs pertenezcan al articulo. Si falta un UID, viene duplicado o no pertenece al articulo, no se borra ninguna imagen.

Despues de borrar las imagenes indicadas, se reordena una sola vez la lista restante.

Respuesta:

```json
{
  "result": {
    "ok": true,
    "deleted": 3
  }
}
```

---
### Crear articulo

POST /api/v3/articulos

request:
```json
{
  "numart":"PRUEBACREAR5",
  "desc":"Prueba para registro de articulos2",
  "clavesat":"XBX",
  "unidad":"CAJA",
  "obs":"prueba para crear articulos, segunda prueba",
  "familia":"SUERX",
  "linea":"21043",
  "categoria":"SEDAL",
  "marca":"ACME",
  "modelo":"1-001",
  "impuesto1":16,
  "impuesto2":0,
  "excento":0,
  "divisa":"P",
  "precio1": 200.50,
  "precio2": 0,
  "precio3": 0,
  "precio4": 0,
  "precio5": 0,
  "fotos":"abrecubetas.jpg\nabrecubetas2.jpg",
  "esmatpelig":0,
  "matpelig":"",
  "activo":1
}
```

response
```json
{
    "id": 379310,
    "created": "2025-07-30 00:16:01",
    "updated": "2025-07-30 00:16:01",
    "numalm": "",
    "numart": "        PRUEBACREAR5",
    "desc": "Prueba para registro de articulos2",
    "marca": "ACME",
    "modelo": "1-001",
    "preciopub": 232.58,
    "clavesat": "XBX",
    "aplicacion": "",
    "codigo": "",
    "unidad": "CAJA",
    "unidefa": "",
    "linea": "21043",
    "familia": "SUERX",
    "categoria": "SEDAL",
    "numdep": "",
    "valdep": "",
    "ubica": "",
    "series": 0,
    "impuesto1": 16,
    "impuesto2": 0,
    "numprov": "",
    "numprov1": "",
    "numprov2": "",
    "numprov3": "",
    "ultcomp": "",
    "ultcomp1": "",
    "ultcomp2": "",
    "ultcomp3": "",
    "ultvent": "",
    "existencia": 0,
    "minimo": 0,
    "maximo": 0,
    "reorden": 0,
    "divisa": "P",
    "precio1": 200.5,
    "precio2": 0,
    "precio3": 0,
    "precio4": 0,
    "precio5": 0,
    "factor1": 0,
    "factor2": 0,
    "factor3": 0,
    "factor4": 0,
    "factor5": 0,
    "Ultcosto": 0,
    "Ultcosto1": 0,
    "Ultcosto2": 0,
    "Ultcosto3": 0,
    "Maxcosto": 0,
    "Costoactua": 0,
    "Costopro": 0,
    "Ventano": 0,
    "Ventanoqty": 0,
    "activo": 0,
    "Ultmaxcost": 0,
    "Ultactcost": 0,
    "Compano": 0,
    "Companoqty": 0,
    "Repyy": 0,
    "cant_defa": 0,
    "excento": 0,
    "preciof": 0,
    "servicio": 0,
    "fechamod": "",
    "obs": "prueba para crear articulos, segunda prueba",
    "usacaduc": 0,
    "usalotes": 0,
    "ventacorte": 0,
    "eskit": 0,
    "uid": 12642,
    "otrosdatos": "",
    "pjedesc": 0,
    "oferta": 0,
    "insumo": 0,
    "peso": 0,
    "largo": 0,
    "ancho": 0,
    "altura": 0,
    "cantxcj": 0,
    "foto": "",
    "fotos": "abrecubetas.jpg\nabrecubetas2.jpg",
    "idmarca": "",
    "preciov2": 0,
    "preciov3": 0,
    "preciov4": 0,
    "preciov5": 0,
    "ppubv2": 0,
    "ppubv3": 0,
    "ppubv4": 0,
    "ppubv5": 0,
    "vol2": 0,
    "vol3": 0,
    "vol4": 0,
    "vol5": 0,
    "statusweb": 0,
    "implocal": 0,
    "fracaranc": "",
    "matpelig": "",
    "embalaje": "",
    "guiaid": "",
    "guiaiddesc": "",
    "esmatpelig": 0,
    "fraccok": 0,
    "imagenes": [
      "demosaitnube.sfo3.cdn.digitaloceanspaces.com/sait-imagenes/abrecubetas.jpg",
      "demosaitnube.sfo3.cdn.digitaloceanspaces.com/sait-imagenes/abrecubetas2.jpg"
    ]
}
```

---
### Leer por Clave

GET /api/v3/articulos/:numart

```json
{
      "id": 337138,
      "created": "2024-09-19 00:04:40",
      "updated": "2024-09-19 00:04:40",
      "numart": "                   1",
      "desc": "ESFERA DE UNICEL NO.1",
      "marca": "SIN MARCA",
      "modelo": "ESFERAS",
      "preciopub": 2.5,
      "clavesat": "13111308",
      "aplicacion": "",
      "fecha": "",
      "codigo": "",
      "unidad": "PZA",
      "unidefa": "",
      "linea": "18002",
      "familia": "   67",
      "categoria": " 1801",
      "numdep": "18",
      "valdep": " 18",
      "ubica": "",
      "series": 0,
      "impuesto1": 8,
      "impuesto2": 0,
      "numprov": "",
      "numprov1": "",
      "numprov2": "",
      "numprov3": "",
      "ultcomp": "",
      "ultcomp1": "",
      "ultcomp2": "",
      "ultcomp3": "",
      "ultvent": "2024-06-19",
      "existencia": 247,
      "minimo": 0,
      "maximo": 0,
      "reorden": 0,
      "divisa": "P",
      "precio1": 2.3148,
      "precio2": 2.3148,
      "precio3": 2.3148,
      "precio4": 2.3148,
      "precio5": 2.3148,
      "factor1": 4.4075,
      "factor2": 4.4075,
      "factor3": 4.4075,
      "factor4": 4.4075,
      "factor5": 4.4075,
      "Ultcosto": 0,
      "Ultcosto1": 0,
      "Ultcosto2": 0,
      "Ultcosto3": 0,
      "Maxcosto": 0,
      "Costoactua": 0,
      "Costopro": 0,
      "Ventano": 0,
      "Ventanoqty": 0,
      "activo": 1,
      "Ultmaxcost": "",
      "Ultactcost": "",
      "Compano": 0,
      "Companoqty": 0,
      "Repyy": 0,
      "cant_defa": 0,
      "excento": 0,
      "preciof": 0,
      "servicio": 0,
      "fechamod": "2024-03-13",
      "obs": "",
      "usacaduc": 0,
      "usalotes": 0,
      "ventacorte": 0,
      "eskit": 0,
      "uid": 3516,
      "otrosdatos": "",
      "pjedesc": 0,
      "oferta": 0,
      "insumo": 0,
      "peso": 0,
      "largo": 0,
      "ancho": 0,
      "altura": 0,
      "cantxcj": 0,
      "foto": "",
      "fotos": "",
      "idmarca": "483",
      "preciov2": 2.3148,
      "preciov3": 2.3148,
      "preciov4": 0,
      "preciov5": 0,
      "ppubv2": 2.5,
      "ppubv3": 2.5,
      "ppubv4": 0,
      "ppubv5": 0,
      "vol2": 0,
      "vol3": 0,
      "vol4": 0,
      "vol5": 0,
      "statusweb": 0,
      "implocal": 0,
      "fracaranc": "",
      "matpelig": "",
      "embalaje": "",
      "guiaid": "",
      "guiaiddesc": "",
      "esmatpelig": 0
    }
```

---
### Actualizar articulo

PUT /api/v3/articulos/:numart

request:
```json
{
  "desc":"Prueba para registro de articulos2",
  "clavesat":"XBX",
  "unidad":"CAJA",
  "obs":"prueba para crear articulos, segunda prueba",
  "familia":"SUERX",
  "linea":"21043",
  "categoria":"SEDAL",
  "marca":"ACME",
  "modelo":"1-001",
  "impuesto1":16,
  "impuesto2":0,
  "excento":0,
  "divisa":"P",
  "precio1": 200.50,
  "precio2": 0,
  "precio3": 0,
  "precio4": 0,
  "precio5": 0,
  "fotos":"abrecubetas.jpg\nabrecubetas2.jpg",
  "esmatpelig":0,
  "matpelig":"",
  "activo":1
}
```

response
```json
{
    "id": 379310,
    "created": "2025-07-30 00:16:01",
    "updated": "2025-07-30 00:16:01",
    "numalm": "",
    "numart": "        PRUEBACREAR5",
    "desc": "Prueba para registro de articulos2",
    "marca": "ACME",
    "modelo": "1-001",
    "preciopub": 232.58,
    "clavesat": "XBX",
    "aplicacion": "",
    "codigo": "",
    "unidad": "CAJA",
    "unidefa": "",
    "linea": "21043",
    "familia": "SUERX",
    "categoria": "SEDAL",
    "numdep": "",
    "valdep": "",
    "ubica": "",
    "series": 0,
    "impuesto1": 16,
    "impuesto2": 0,
    "numprov": "",
    "numprov1": "",
    "numprov2": "",
    "numprov3": "",
    "ultcomp": "",
    "ultcomp1": "",
    "ultcomp2": "",
    "ultcomp3": "",
    "ultvent": "",
    "existencia": 0,
    "minimo": 0,
    "maximo": 0,
    "reorden": 0,
    "divisa": "P",
    "precio1": 200.5,
    "precio2": 0,
    "precio3": 0,
    "precio4": 0,
    "precio5": 0,
    "factor1": 0,
    "factor2": 0,
    "factor3": 0,
    "factor4": 0,
    "factor5": 0,
    "Ultcosto": 0,
    "Ultcosto1": 0,
    "Ultcosto2": 0,
    "Ultcosto3": 0,
    "Maxcosto": 0,
    "Costoactua": 0,
    "Costopro": 0,
    "Ventano": 0,
    "Ventanoqty": 0,
    "activo": 0,
    "Ultmaxcost": 0,
    "Ultactcost": 0,
    "Compano": 0,
    "Companoqty": 0,
    "Repyy": 0,
    "cant_defa": 0,
    "excento": 0,
    "preciof": 0,
    "servicio": 0,
    "fechamod": "",
    "obs": "prueba para crear articulos, segunda prueba",
    "usacaduc": 0,
    "usalotes": 0,
    "ventacorte": 0,
    "eskit": 0,
    "uid": 12642,
    "otrosdatos": "",
    "pjedesc": 0,
    "oferta": 0,
    "insumo": 0,
    "peso": 0,
    "largo": 0,
    "ancho": 0,
    "altura": 0,
    "cantxcj": 0,
    "foto": "",
    "fotos": "abrecubetas.jpg\nabrecubetas2.jpg",
    "idmarca": "",
    "preciov2": 0,
    "preciov3": 0,
    "preciov4": 0,
    "preciov5": 0,
    "ppubv2": 0,
    "ppubv3": 0,
    "ppubv4": 0,
    "ppubv5": 0,
    "vol2": 0,
    "vol3": 0,
    "vol4": 0,
    "vol5": 0,
    "statusweb": 0,
    "implocal": 0,
    "fracaranc": "",
    "matpelig": "",
    "embalaje": "",
    "guiaid": "",
    "guiaiddesc": "",
    "esmatpelig": 0,
    "fraccok": 0,
    "imagenes": [
      "demosaitnube.sfo3.cdn.digitaloceanspaces.com/sait-imagenes/abrecubetas.jpg",
      "demosaitnube.sfo3.cdn.digitaloceanspaces.com/sait-imagenes/abrecubetas2.jpg"
    ]
}
```

---
### Buscar Artículos

GET /api/v3/articulos?variables

| Variable  | Significado                                                |
|-----------|------------------------------------------------------------|
| offset    | A partir de que registro iniciar búsqueda. Default 0       |
| limit     | Cuantos registros obtener. Default 100                     |
| order     | Orden deseado. Disponibles:updated,id,numart               |
| q         | Palabras a buscar                                          |
| divisa    | P = pesos - D = Dolares                                    |
| statusweb | 0 = no disponible en tienda web - 1 = disponible en tienda |
| categoria | numero de categoria de articulo                            |
| familia   | numero de familia de articulo                              |
| linea     | numero de linea de articulo                                |
| numprov   | numero de proveedor                                        |
| numdep    | numero de departamento                                     |

| Ejemplo de Búsqueda           | Ruta                                |
|-------------------------------|-------------------------------------|
| Buscar los primeros 25 arts   | /api/v3/articulos?limit=25          |
| Buscar rótulas                | /api/v3/articulos?q=rotula          |
| Buscar rótulas para Suburban  | /api/v3/articulos?q=rotula+suburban |
| Buscar por divisa en dolares  | /api/v3/articulos?divisa=D          |
| Buscar por disponibilidad web | /api/v3/articulos?statusweb=1       |
| Leer artículo por clave       | /api/v3/articulos/clave/MOO-K6145   |
| Leer artículo por id          | /api/v3/articulos/3929              |

```json
[
{
      "id": 337138,
      "created": "2024-09-19 00:04:40",
      "updated": "2024-09-19 00:04:40",
      "numart": "                   1",
      "desc": "ESFERA DE UNICEL NO.1",
      "marca": "SIN MARCA",
      "modelo": "ESFERAS",
      "preciopub": 2.5,
      "clavesat": "13111308",
      "aplicacion": "",
      "fecha": "",
      "codigo": "",
      "unidad": "PZA",
      "unidefa": "",
      "linea": "18002",
      "familia": "   67",
      "categoria": " 1801",
      "numdep": "18",
      "valdep": " 18",
      "ubica": "",
      "series": 0,
      "impuesto1": 8,
      "impuesto2": 0,
      "numprov": "",
      "numprov1": "",
      "numprov2": "",
      "numprov3": "",
      "ultcomp": "",
      "ultcomp1": "",
      "ultcomp2": "",
      "ultcomp3": "",
      "ultvent": "2024-06-19",
      "existencia": 247,
      "minimo": 0,
      "maximo": 0,
      "reorden": 0,
      "divisa": "P",
      "precio1": 2.3148,
      "precio2": 2.3148,
      "precio3": 2.3148,
      "precio4": 2.3148,
      "precio5": 2.3148,
      "factor1": 4.4075,
      "factor2": 4.4075,
      "factor3": 4.4075,
      "factor4": 4.4075,
      "factor5": 4.4075,
      "Ultcosto": 0,
      "Ultcosto1": 0,
      "Ultcosto2": 0,
      "Ultcosto3": 0,
      "Maxcosto": 0,
      "Costoactua": 0,
      "Costopro": 0,
      "Ventano": 0,
      "Ventanoqty": 0,
      "activo": 1,
      "Ultmaxcost": "",
      "Ultactcost": "",
      "Compano": 0,
      "Companoqty": 0,
      "Repyy": 0,
      "cant_defa": 0,
      "excento": 0,
      "preciof": 0,
      "servicio": 0,
      "fechamod": "2024-03-13",
      "obs": "",
      "usacaduc": 0,
      "usalotes": 0,
      "ventacorte": 0,
      "eskit": 0,
      "uid": 3516,
      "otrosdatos": "",
      "pjedesc": 0,
      "oferta": 0,
      "insumo": 0,
      "peso": 0,
      "largo": 0,
      "ancho": 0,
      "altura": 0,
      "cantxcj": 0,
      "foto": "",
      "fotos": "",
      "idmarca": "483",
      "preciov2": 2.3148,
      "preciov3": 2.3148,
      "preciov4": 0,
      "preciov5": 0,
      "ppubv2": 2.5,
      "ppubv3": 2.5,
      "ppubv4": 0,
      "ppubv5": 0,
      "vol2": 0,
      "vol3": 0,
      "vol4": 0,
      "vol5": 0,
      "statusweb": 0,
      "implocal": 0,
      "fracaranc": "",
      "matpelig": "",
      "embalaje": "",
      "guiaid": "",
      "guiaiddesc": "",
      "esmatpelig": 0
    },
    {}...
]
```
