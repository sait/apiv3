# Articulos

Rutas de Artículos

| Acción                                | Ruta                              |
|---------------------------------------|-----------------------------------|
| [Crear articulo](#crear-articulo)     | POST /api/v3/articulos            |
| [Leer por clave](#leer-por-clave)     | GET  /api/v3/articulos/:numart    |
| [Actualizar](#actualizar-articulo)    | PUT  /api/v3/articulos/:numart    |
| [Listar articulos](#buscar-artículos) | GET  /api/v3/articulos?filters... |

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
