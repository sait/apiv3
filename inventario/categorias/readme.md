## Categorias

Rutas de categorias

| Acción                              | Ruta                                |
|-------------------------------------|-------------------------------------|
| [Crear](#crear-categoria)           | POST  /api/v3/categorias            |
| [Leer](#leer-categoria)             | GET   /api/v3/categorias/:numcat    |
| [Actualizar](#actualizar-categoria) | PUT   /api/v3/categorias/:numcat    |
| [Listado](#listar-categorias)       | GET   /api/v3/categorias?filters... |

Campos de categorias

| Campo   | tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | id incremental disponible solo através de SAIT API |
| created | datetime | timestamp del momento de creación                  |
| updated | datetime | timestamp ultima actualizacion                     |
| numcat  | string   | Clave de la Categoría.                             |
| nomcat  | string   | Nombre de la Categoría.                            |
| margen  | decimal  | Margen de ganancia                                 |

---
### Crear Categoria

POST /api/v3/categorias

request
```json
{
  "numcat": "1002",
  "nomcat": "CATEGORIA PRUEBA",
  "margen": 0
}
```

response
```json
{
  "id": 247,
  "created": "2025-07-01 18:22:20",
  "updated": "2025-07-01 18:22:20",
  "numcat": " 1002",
  "nomcat": "CATEGORIA PRUEBA",
  "margen": 0
}
```

---
### Leer Categoria

GET /api/v3/categorias/:numcat

response
```json
{
  "id": 4,
  "created": "2025-08-01 15:34:41",
  "updated": "2025-08-01 15:34:41",
  "numcat": " 1104",
  "nomcat": "Borradores",
  "margen": 0
}
```

---
### Actualizar Categoria

PUT /api/v3/categorias/:numcat

request
```json
{
  "nomcat": "CATEGORIA PRUEBA UPD",
  "margen": 0
}
```

response
```json
"UPDATED"
```

---
### Listar Categorias

GET /api/v3/categorias?filters

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,numcat         |
| q        | Palabras a buscar                                    |

```json
[
  {
    "id": 1,
    "created": "2025-08-01 15:34:36",
    "updated": "2025-08-01 15:34:36",
    "numcat": " 1101",
    "nomcat": "Tijeras",
    "margen": 0
  },
  {
    "id": 2,
    "created": "2025-08-01 15:34:36",
    "updated": "2025-08-01 15:34:36",
    "numcat": " 1102",
    "nomcat": "Sacapuntas",
    "margen": 0
  },
  {}...
]
```