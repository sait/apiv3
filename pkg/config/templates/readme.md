## Templates

Rutas de templates

| Accion       | Ruta                                |
|--------------|-------------------------------------|
| [Crear]      | POST   /api/v3/templates            |
| [Leer]       | GET    /api/v3/templates/:id        |
| [Actualizar] | PUT    /api/v3/templates/:id        |
| [Borrar]     | DELETE /api/v3/templates/:id        |
| [Listar]     | GET    /api/v3/templates?filters... |

Campos de templates

| Campo   | tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | id incremental disponible solo através de SAIT API |
| created | datetime | timestamp del momento de creación                  |
| updated | datetime | timestamp ultima actualizacion                     |
| type    | string   | define el tipo de documento a imprimir             |
| title   | string   | descripcion del documento                          |
| jscode  | string   | codigo js de template                              |
| ispdf   | int      | define si es pdf o un txt (1: PDF, 0: TXT)         |

---
### Crear

POST /api/v3/templates

request:
```json
{
  "type":"C",
  "title":"prueba templates",
  "jscode":"templateReceiptPrinterEncoder.js"
}
```

response:
```json
"CREATED"
```

---
### Leer

GET /api/v3/templates/:id

response:
```json
{
    "id": 1,
    "created": "2025-09-08 23:38:24",
    "updated": "2025-09-08 23:38:24",
    "type": "C",
    "title": "prueba templates",
    "jscode": "templateReceiptPrinterEncoder...",
    "ispdf": 0
}
```

---
### Actualizar

PUT /api/v3/templates/:id

request:
```json
{
  "type":"D",
  "title":"prueba templates2",
  "jscode":"templateReceiptPrinterEncoder.js"
}
```

response:
```json
"UPDATED"
```

---
### Borrar

DELETE /api/v3/templates/:id

response:
```json
"DELETED"
```

---
### Listar

GET /api/v3/templates?filters...

| Variable | Significado                                           |
|----------|-------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0  |
| limit    | Cuantos registros obtener. Default 100                |
| order    | Orden deseado. Disponibles:updated,id,numzona,nomzona |
| q        | Palabras a buscar en title                            |
| type     | filtro por tipo de documento                          |

response:
```json
[
    {
        "id": 1,
        "created": "2025-09-08 23:38:24",
        "updated": "2025-09-08 23:38:24",
        "type": "C",
        "title": "prueba templates",
        "jscode": "templateReceiptPrinterEncoder...",
        "ispdf": 0
    },
    {}...
]
```