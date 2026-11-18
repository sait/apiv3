## lineas

Rutas de lineas

| Acción                          | Ruta                           |
|---------------------------------|--------------------------------|
| [Crear](#crear-linea)           | POST  /api/v3/lineas           |
| [Leer](#leer-linea)             | GET   /api/v3/lineas/:numlin   |
| [Actualizar](#actualizar-linea) | PUT   /api/v3/lineas/:numlin   |
| [Listado](#listar-lineas)       | GET  /api/v3/lineas?filters... |
| [Totales](#totales-de-lineas)   | GET   /api/v3/lineas?totalizar=true&filters... |

Campos de lineas

| Campo   | tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | id incremental disponible solo através de SAIT API |
| created | datetime | timestamp del momento de creación                  |
| updated | datetime | timestamp ultima actualizacion                     |
| numlin  | string   | Clave de la linea.                                 |
| nomlin  | string   | Nombre de la linea.                                |
| margen  | decimal  | Margen de ganancia de la linea                     |

---
### Crear linea

POST /api/v3/lineas

request
```json
{
  "numlin": "1002",
  "nomlin": "PRUEBA CREATE",
  "margen": 3
}
```

response
```json
{
  "id": 747,
  "created": "2025-07-01 18:46:22",
  "updated": "2025-07-01 18:46:22",
  "numlin": " 1002",
  "nomlin": "PRUEBA CREATE",
  "margen": 3
}
```

---
### Leer linea

GET /api/v3/lineas/:numlin

response
```json
{
  "id": 73,
  "created": "2025-08-01 15:31:34",
  "updated": "2025-08-01 15:31:34",
  "numlin": "11020",
  "nomlin": "Liquidos",
  "margen": 0
}
```

---
### Actualizar linea

PUT /api/v3/lineas/:numlin

request
```json
{
  "nomlin": "PRUEBA CREATE UPD",
  "margen": 5
}
```

response
```json
"UPDATED"
```

---
### Listar Lineas

GET /api/v3/lineas?filters

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,numlin         |
| q        | Palabras a buscar                                    |

```json
[
  {
    "id": 71,
    "created": "2025-08-01 15:31:34",
    "updated": "2025-08-01 15:31:34",
    "numlin": "11009",
    "nomlin": "Juegos de Geometría",
    "margen": 0
  },
  {
    "id": 149,
    "created": "2025-08-01 15:31:44",
    "updated": "2025-08-01 15:31:44",
    "numlin": "11012",
    "nomlin": "Reglas y Escuadras",
    "margen": 0
  },
  {}...
]
```

---

### Totales de lineas

GET /api/v3/lineas?totalizar=true&variables

Regresa la cantidad de lineas que cumplen el mismo filtro `q` de [Listar Lineas](#listar-lineas) (busca en `nomlin`). No se aplican `limit`, `offset` ni `order`.

| Ejemplo                    | Ruta                                       |
|----------------------------|--------------------------------------------|
| Total de lineas            | /api/v3/lineas?totalizar=true              |
| Total que contienen "tor"  | /api/v3/lineas?totalizar=true&q=tor        |

response:
```json
{
  "items": "25"
}
```
