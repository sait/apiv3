# SAIT API 3.0

## indice

- [Rutas](#indice-de-rutas)
- [Introduccion](#introducción)
- [Encabezados](#encabezados)
- [Respuestas](#respuestas)
- [Pruebas](#pruebas)

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
X-sait-api-key: frizispe9swlhim0
```

### Formato de Datos 

La API puede manejar el formato JSON o XML según la preferencia del programador.
- El formato default es JSON.
- Si desea manejar el formato XML debe indicar en el Request el Header: **Content-Type:application/xml**

```yaml
GET http://miempresa.saitnube.com/api/v3/clientes/13
X-sait-api-key: frizispe9swlhim0
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

---
## Pruebas

### URL Base

```
test.saitnube.com
```

### Tokens para pruebas

En saitnube contamos con 2 tipos de accesos:
- apikeys
- token de acceso otorgado por login/autenticacion

#### apikey

```
X-sait-api-key:fqkzlbklwliaeo1r
```

#### Login Authentication

POST api/v3/login

```json
{
    "numuser":"TEST",
    "password":"5348800"
}
```

token acceso:
```
X-sait-token:{token de acceso otorgado por autenticacion}
```