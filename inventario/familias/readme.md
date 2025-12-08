## familias

Rutas de familias

| Acción                            | Ruta                             |
|-----------------------------------|----------------------------------|
| [Crear](#crear-familia)           | POST  /api/v3/familias           |
| [Leer](#leer-familia)             | GET   /api/v3/familias/:numfam   |
| [Actualizar](#actualizar-familia) | PUT   /api/v3/familias/:numfam   |
| [Listado](#listar-familias)       | GET  /api/v3/familias?filters... |

Campos de familias

| Campo   | tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | id incremental disponible solo através de SAIT API |
| created | datetime | timestamp del momento de creación                  |
| updated | datetime | timestamp ultima actualizacion                     |
| numfam  | string   | Clave de la familia.                               |
| nomfam  | string   | Nombre de la familia.                              |
| margen  | decimal  | Margen de ganancia de la familia                   |

---
### Crear familia

POST /api/v3/familias

request
```json
{
  "numfam": "1002",
  "nomfam": "PRUEBA CREATE",
  "margen": 5
}
```

response
```json
{
  "id": 595,
  "created": "2025-07-01 18:41:37",
  "updated": "2025-07-01 18:41:37",
  "numfam": " 1002",
  "nomfam": "PRUEBA CREATE",
  "margen": 5
}
```

---
### Leer familia

GET /api/v3/familias/:numfam

response
```json
{
    "id": 45,
    "created": "2025-08-01 15:33:33",
    "updated": "2025-08-01 15:33:33",
    "numfam": "    8",
    "nomfam": "LAROUSSE",
    "margen": 0
  }
```

---
### Actualizar familia

PUT /api/v3/familias/:numfam

request
```json
{
  "nomfam": "DIDACTICO UPD",
  "margen": 0
}
```

response
```json
"UPDATED"
```

### Listar Familias

GET /api/v3/familias?filters

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,numfam         |
| q        | Palabras a buscar                                    |

```json
[
  {
    "id": 39,
    "created": "2025-08-01 15:33:28",
    "updated": "2025-08-01 15:33:28",
    "numfam": "    2",
    "nomfam": "EDUCATODO",
    "margen": 0
  },
  {
    "id": 40,
    "created": "2025-08-01 15:33:28",
    "updated": "2025-08-01 15:33:28",
    "numfam": "    3",
    "nomfam": "BACO",
    "margen": 0
  },
  {}...
]
```
