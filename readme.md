# SAIT API 3.0

## indice

- [Rutas](#indice-de-rutas)
- [Introduccion](#introducción)
- [Encabezados](#encabezados)
- [Respuestas](#respuestas)

## Credenciales de Acceso para realizar pruebas

Para realizar pruebas de la API, utilice las siguientes credenciales de acceso:
```
host: https://test.saitnube.com/api/v3/
header: X-sait-api-key:****************
```

Ejemplos:
```
curl https://test.saitnube.com/api/v3/version
{"result":"3.0.0","error":""}

curl https://test.saitnube.com/api/v3/hello
{"result":"Hello World!","error":""}

curl -si -H "X-sait-api-key: ****************" "https://test.saitnube.com/api/v3/clientes?q=home&limit=10&order=-id"

{"result":
	[

		{"id":3953,"created":"2024-06-29 12:36:47","updated":"2024-08-02 09:11:58",
		"numcli":"  F10","nomcli":"HOME DEPOT MEXICO","calle":"RICARDO MARGAIN ZOZAYA","numext":"605",
		"colonia":"SANTA ENGRACI","ciudad":"SAN PEDRO GARZA","estado":"NUEVO LEON","pais":"MEXICO","cp":"66267"}
	],
"error":""
}

curl -si -H "X-sait-api-key: ****************" "https://test.saitnube.com/api/v3/clientes?q=farmacia&limit=10&order=-id"
{"result":
	[
		{"id":4281,"created":"2024-06-29 12:36:47","updated":"2025-09-09 08:45:33",
		"numcli":"  B83","nomcli":"FARMACIAS DE SIMILARES","calle":"ALEMANIA","numext":"10",
		"colonia":"INDEPENDENCIA","ciudad":"BENITO JUAREZ","estado":"CIUDAD DE MEXICO","pais":"MEXICO","cp":"03630"},

		{"id":481,"created":"2024-06-29 12:36:46","updated":"2025-11-13 10:55:47",
		"numcli":"  511","nomcli":"DISTRIBUIDORA DE MIS FARMACIAS","calle":"","numext":"",
		"colonia":"","ciudad":"","estado":"","pais":"MEXICO","cp":"22010"}
	],
"error":""
}
```

## Indice de Rutas
- Auth
    - [Permisos de usuario y Apikeys](./auth/readme.md)
- Ventas
    - [Clientes](./ventas/clientes/readme.md)
    - [Vendedores](./ventas/vendedores/readme.md)
    - [zonas](./ventas/zonas/readme.md)
    - [Facturas](./ventas/facturas/readme.md)
    - [Cotizaciones](./ventas/cotizaciones/readme.md)
    - [Pedidos](./ventas/pedidos/readme.md)
    - [Calcular precios](./ventas/calcularprecios/readme.md)
- Caja
    - [Movimientos de caja](./caja/readme.md)
    - [Cortes](./caja/cortes/readme.md)
    - [Conceptos de caja](./caja/conccaja/readme.md)
    - [Beneficiarios](/caja/beneficiarios/readme.md)
- Inventario
    - [Artículos](./inventario/articulos/readme.md)
        - [Imagenes](./inventario/imagenes/readme.md)
    - [Unidades](./inventario/unidades/readme.md)
    - [ClaveSat](./inventario/satprod/readme.md)
    - [Categorias](./inventario/categorias/readme.md)
    - [Familias](./inventario/familias/readme.md)
    - [Lineas](./inventario/lineas/readme.md)
    - [departamentos en inventario de articulos](./inventario/deptos/readme.md)
    - [Existencias](./inventario/multialm/readme.md)
- Cobranza
    - [Catalogo de movimientos](./cobranza/catmov/readme.md)
    - [Estado de cuente de cliente](./cobranza/estadocuenta/readme.md)
- otros 
    - [Almacenes](./utils/almacenes/readme.md)
    - [Configuracion](./pkg/config/readme.md)
        - [Templates/Plantillas](./pkg/config/templates/readme.md) 
        - [Series](./pkg/series/readme.md)


## Introducción

SAIT Software Administrativo cuenta con una API que es usada para conexión con **Sistemas de Terceros** por ejemplo:
- Tiendas de Comercio Electrónico
- Aplicaciones de Venta en Ruta
- Portal de Facturación en Línea

La conexión con **Sistemas de Terceros** se logra mediante:
- Llamadas  **API Restful** tradicionales a los recursos de SAIT **(HTTP POST,READ,PUT,DELETE = Create,Read,Update,Delete )**
- Generación **Webhooks** hacia el otro sistema, mediante un POST cada vez que se recibe un evento del tipo: Create, Update o Delete


### Módulos de Operación de SAIT

En SAIT, los recursos los agrupamos según el módulo de operación del sistema.

- Ventas
- Cobranza
- Caja
- Inventario
- Compras
- Cuentas por Pagar
- Bancos
- Contabilidad

## Encabezados

### Acceso a la API

El acceso a la API se otorga mediante una API Key única para cada **Sistema de Tercero** y que debe indicarse en el Request mediante el Header: **X-sait-api-key** por ejemplo:

```yaml
GET http://miempresa.saitnube.com/api/v3/clientes/13
X-sait-api-key: ****************
```

### Formato de Datos 

La API puede manejar el formato JSON o XML según la preferencia del programador.
- El formato default es JSON.
- Si desea manejar el formato XML debe indicar en el Request el Header: **Content-Type:application/xml**

```yaml
GET http://miempresa.saitnube.com/api/v3/clientes/13
X-sait-api-key: ****************
Content-Type: application/xml
```

## Respuestas

Los códigos HTTP de respuesta son los típicos de las API Restful, según el tipo de acción CRUD:

| Request    | Acción                             | Código | Significado                              |
|------------|------------------------------------|--------|------------------------------------------|
| **POST**   | (C) Create - Crear un recurso      | 201    | Recurso ha sido creado.                  |
|            |                                    | 400    | Error de validación al crear recurso     |
| **GET**    | (R) Read - Leer un recurso         | 200    | Recurso ha sido leido                    |
|            |                                    | 404    | Recurso NO exite                         |
| **PUT**    | (U) Update - Actualizar un recurso | 200    | Recurso ha sido actualizado              |
|            |                                    | 404    | Recurso no existe                        |
|            |                                    | 400    | Error de validación al acualizar recurso |
| **DELETE** | (D) Delete - Borrar un recurso     | 200    | Recurso ha sido borrado                  |
|            |                                    | 404    | Recurso no existe                        |
| **GET**    | (L) List - Listar recursos         | 200    | Recursos han sido listados               |

Además de los código errores generales:

| Código | Significado                   |
|--------|-------------------------------|
| 500    | Error interno de la API       |
| 401    | Acceso No Autorizado a la API |


Las respuestas de la API inlcuyen siempre 2 campos y opcionalmente uno adicional en el body:
- **result:** es un objeto y representa el recurso leido o creado.
- **error:** es un string y representa el mensaje de error en caso de falla.
- **errobj:** es opcional, solo se usa en POST y PUT y representa un objeto conteniendo los datos que NO cumplieron con las validaciones necesarias.

Ejemplo de recurso creado 201
```json
{
    "result": {
        "id":1,
        "nomcli":"Bimbo de Mexico"
    },
    "error": ""
}
```

Ejemplo de recurso actualizado 200
```json
{
    "result": "UPDATED",
    "error": ""
}
```

Ejemplo de falla 400
```json
{
    "result": null,
    "error": "No es posible emitir factura anteriores a 72 horas."
}
```

Ejemplo de falla de validacions 400
```json
{
    "result": null,
    "error": "Fallas de Validacion de datos.",
    "errobj": {
        "nombre":"Excede de 50 posiciones",
        "estado":"No es valido"
    }
}
```