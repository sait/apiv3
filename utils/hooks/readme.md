## Hooks

Rutas de Hooks

| Accion                           | Ruta                                  |
| -------------------------------- | ------------------------------------- |
| [Crear](#crear-hook)             | POST /api/v3/hooks                    |
| [Leer por id](#leer-hook-por-id) | GET  /api/v3/hooks/:id                |
| [Actualizar](#actualizar-hook)   | PUT  /api/v3/hooks/:id                |
| [Eliminar](#eliminar-hook)       | DELETE  /api/v3/hooks/:id             |
| [Buscar](#buscar-hooks)          | GET  /api/v3/hooks?filters...         |


Campos de Hooks

| Campo       | Significado                                                |
| ----------- | ---------------------------------------------------------- |
| id          | id incremental disponible solo através de SAIT API         |
| created     | timestamp del momento de creación                          |
| updated     | timestamp ultima actualizacion                             |
| appname     | nombre de la aplicacion a donde llegaran los eventos       |
| url         | Url de aplicacion a los que llegaran los eventos           |
| token       | token de acceso a saitnube                                 |
| activo      | 1 = activo - 0 = desactivado                               |
| eventos     | Tipo de evento que recibira la aplicacion                  |
| ultevento   | Ultimo evento recibido de la aplicacion                    |
| errores     | lista de errores encontrados                               |

---
### Crear Hook

POST /api/v3/hooks

request:
```json
{
  "appname"   : "Factura",
  "url"       : "www.factura123.mx",
  "token"     : "123456",
  "activo"    : 1,
  "eventos"   : "MODCLI",
  "ultevento" : 0
}
```

response:
```json
{
    "id": 2,
    "created" : "",
    "updated" : "",
    "appname" : "Factura",
    "url" : "www.factura123.mx",
    "token" : "123456",
    "activo" : 1,
    "eventos" : "event",
    "ultevento" : 0,
    "actualizado" : ""
}
```

---
### Leer Hook por Id

GET /api/v3/hooks/:id

response:
```json
{
    "id" : 1,
    "created" : "2024-09-12 16:33:07",
    "updated" : "2024-09-12 16:33:07",
    "appname" : "Factura",
    "url" : "www.factura123.mx",
    "token" : "123456",
    "activo" : 1,
    "eventos" : "event",
    "ultevento" : 0,
    "actualizado" : ""
}
```

---
### Actualizar Hook

PUT /api/v3/hooks/:id

request:
```json
{
    "appname" : "Factura 2",
    "url" : "www.factura123.mx",
    "token" : "123456",
    "activo" : 1,
    "eventos" : "ADDNC",
    "ultevento" : 12,
}
```

response:
```json
"UPDATED"
```

---
### Eliminar Hook

PUT /api/v3/hooks/:id

response:
```json
"DELETED"
```



---
### Buscar Hooks

GET /api/v3/hooks?filters...

| Variable | Significado                                             |
| -------- | ------------------------------------------------------- |
| offset   | A partir de que registro iniciar búsqueda. Default 0    |
| limit    | Cuantos registros obtener. Default 100                  |
| q        | buscar por nombre de aplicacion                         |

response:
```json
[
    {
      "id" : 4,
      "created" : "2024-09-12 16:33:07",
      "updated" : "2024-09-12 16:33:07",
      "appname" : "Factura",
      "url" : "www.factura123.mx",
      "token" : "123456",
      "activo" : 1,
      "eventos" : "event",
      "ultevento" : 0,
      "errores" : [
            {
                "id" : 1,
                "created" : "2024-09-12 23:57:56",
                "updated" : "2024-09-12 23:57:56",
                "idhook" : 4,
                "idevento" : "12",
                "code" : 500,
                "message" : "esta es una prueba para GET de hooks con lista de errores ",
                "procesando" : ""
            }
        ]
    }
]
```



