Rutas

| Acción                          | Ruta                             |
|---------------------------------|----------------------------------|
| [Crear](#crear-series)          | POST   /api/v3/series            |
| [Leer](#leer-serie)             | GET    /api/v3/series/:id        |
| [Actualizar](#actualizar-serie) | PUT    /api/v3/series/:id        |
| [Borrar](#borrar-serie)         | DELETE /api/v3/series/:id        |
| [Listado](#listar-series)       | GET    /api/v3/series?filters... |

Campos de sigfolios

| Campo    | tipo     | Significado                                        |
|----------|----------|----------------------------------------------------|
| id       | int      | id incremental disponible solo através de SAIT API |
| created  | datetime | timestamp del momento de creación                  |
| updated  | datetime | timestamp ultima actualizacion                     |
| tipo     | varchar  | descripcion de serie ("cotizaciones", "pedidos")   |
| serie    | varchar  | identificador de consecutivo                       |
| folio    | varchar  | numero consecutivo                                 |
| usuarios | varchar  | usuarios con acceso al registro                    |

---
### Crear series

POST /api/v3/series

request
```json
{
    "tipo":"pedidos",
    "serie":"PX",
    "usuarios":[
        "VE1",
        "MSL"
    ],
}
```

response
```json
{
    "id": 8527,
    "created": "2025-10-29 00:11:36",
    "updated": "2025-10-29 00:11:36",
    "tipo": "pedidos",
    "serie": "PX",
    "folio": "",
    "usuarios": [
      "  VE1",
      "  MSL"
    ]
}
```

---
### Leer serie

GET /api/v3/series/:id

response
```json
{
    "id" : 1,
    "created" : "2025-07-01 18:46:22",
    "updated" : "2025-07-01 18:46:22",
    "tipo" : "pedidos",
    "serie" : "T",
    "folio" : "100",
    "usuarios": [
      "  VE1",
      "  MSL"
    ]
}
```

---
### Actualizar serie

PUT /api/v3/series/:id

request
```json
{
    "folio":"500",
    "usuarios":[
        "VE1",
        "MSL"
    ],
}
```

response
```json
{
    "id": 8527,
    "created": "2025-10-29 00:11:36",
    "updated": "2025-10-29 00:11:36",
    "tipo": "pedidos",
    "serie": "PX",
    "folio": "500",
    "usuarios": [
      "  VE1",
      "  MSL"
    ]
}
```

---
### Borrar serie

DELETE /api/v3/series/:id

response
```json
"DELETED"
```

---
### Listar series

GET /api/v3/series?filters...

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,tipo,serie     |
| q        | Palabras a buscar (tipo,serie)                       |
| tipo     | Filtro por tipo                                      |

response
```json
[
    {
        "tipo" : "pedidos",
        "serie" : "T",
        "folio" : 0,
        "usuarios": [
            "  VE1",
            "  MSL"
        ]
    },
    {}...
]
```