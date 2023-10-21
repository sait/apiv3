# SAIT API 3.0

Tabla de Contenido:
- [Introducción](#introducción)
- [Encabezados](#encabezados)
- [Respuestas](#respuestas)
- [Recursos](#recursos)


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
| ---------- | ---------------------------------- | ------ | ---------------------------------------- |
| **POST**   | (C) Create - Crear un recurso      | 201    | Recurso ha sido creado.                  |
|            |                                    | 400    | Error de validación al crear recurso     |
| **GET**    | (R) Read - Leer un recurso         | 200    | Recurso ha sido leido                    |
|            |                                    | 404    | Recurso NO exite                         |
| **PUT**    | (U) Update - Actualizar un recurso | 200    | Recurso ha sido actualizado              |
|            |                                    | 404    | Recurso no existe                        |
|            |                                    | 400    | Error de validación al acualizar recurso |
| **DELETE** | (D) Delete - Borrar un recurso     | 200    | Recurso ha sido borrado                  |
|            |                                    | 404    | Recurso no existe                        |

Además de los código errores generales:

| Código | Significado                   |
| ------ | ----------------------------- |
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



### Respuestas por Tipo de Acción

En caso de éxito en la llamada, el valor de result será dependiendo del tipo de acción, ver la siguiente tabla:

| Verbo     | Acción    | Código | Valor regresado en "result"     | Ejemplo                                                                           |
| --------- | --------- | ------ | ------------------------------- | --------------------------------------------------------------------------------- |
| POST      | Create    | 201    | Recurso creado                  | {"id":1,"created":"2023-02-07 21:37:46","numcli":"10","nomcli":"Bimbo de Mexico"} |
| GET       | Read      | 200    | Recurso leido                   | {"id":1,"created":"2023-02-07 21:37:46","numcli":"10","nomcli":"Bimbo de Mexico"} |
| PUT       | Update    | 200    | UPDATED                         | "UPDATED"                                                                         |
| DELETE    | Delete    | 200    | DELETED                         | "DELETED"                                                                         |
| GET ?cond | Read Many | 200    | Arreglo de Recursos solicitados | [ {recurso1},{recurso2},{recurso3} ]                                              |



## Recursos

- [Clientes](./ventas/clientes/readme.md)
    - [Crear](./ventas/clientes/readme.md#crear-cliente)
    - [Actualizar](./ventas/clientes/readme.md#actualizar-cliente)
    - [Leer por Id](./ventas/clientes/readme.md#leer-cliente-por-id)
    - [Leer por Clave](./ventas/clientes/readme.md#leer-cliente-por-clave)

- [Artiuclos](./inventarios/articulos/readme.md)

Algunos recursos con los que cuenta el programador en SAIT son:
- Ventas
    - Vendedores
    - Clientes
    - Clientes Eventuales
    - Sucursales de Clientes
- Inventario
    - Articulos
    - Precios
    - Existencias
    - Lineas
    - Familias
    - Categorias
    - Unidades de Wmpaque
- Otros
    - Usuarios
    - Grupos de Usuario
    - Sucursales de la Empresa
    - Series de Documentos

