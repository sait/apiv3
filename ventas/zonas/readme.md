## Zonas

Rutas de Zonas

| Accion                         | Ruta                              |
|--------------------------------|-----------------------------------|
| [Crear](#crear-zonas)          | POST   /api/v3/zonas              |
| [Leer](#leer-zona)             | GET    /api/v3/zonas/:numzona     |
| [Actualizar](#actualizar-zona) | PUT    /api/v3/zonas/:numzona     |
| [Borrar](#borrar-zona)         | DELETE /api/v3/zonas/:numzona     |
| [Buscar](#buscar-zonas)        | GET    /api/v3/zonas?variables... |

Campos de los Zonas

| Campo   | tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | id incremental disponible solo através de SAIT API |
| created | datetime | timestamp del momento de creación                  |
| updated | datetime | timestamp ultima actualizacion                     |
| numzona | varchar  | Identificador de la zona                           |
| nomzona | varchar  | Nombre de la zona                                  |


---
### Crear Zonas

POST /api/v3/zonas

request:
```json
{
  "numzona"  : "10AB",
  "nomzona"  : "10 de abril"
}
```

response:
```json
{
  "id"      : 8,
  "created" : "2024-10-30 17:36:09",
  "updated" : "2024-10-30 17:36:09",
  "numzona" : " 10AB",
  "nomzona" : "10 de abril"
}
```

---
### Leer Zona

GET /api/v3/zonas/10AB

response:
```json
{
  "id"      : 8,
  "created" : "2024-10-30 17:36:09",
  "updated" : "2024-10-30 17:36:09",
  "numzona" : " 10AB",
  "nomzona" : "10 de abril"
}
```

---
### Actualizar Zona

PUT /api/v3/zonas/10AB

request:
```json
{
	"nomzona"  : "10 de abril2"
}
```

response:
```json
"UPDATED"
```

---
### Borrar Zona

DELETE /api/v3/zonas/10AB
response:
```json
"DELETED"
```

---
### Buscar Zonas

GET /api/v3/zonas?variables

| Variable | Significado                                           |
|----------|-------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0  |
| limit    | Cuantos registros obtener. Default 100                |
| order    | Orden deseado. Disponibles:updated,id,numzona,nomzona |
| q        | Palabras a buscar                                     |

| Ejemplo de Búsqueda                | Ruta                    |
|------------------------------------|-------------------------|
| Limitar campos                     | /api/v3/zonas?limit=100 |
| indicar desde que registro empezar | /api/v3/zonas?offset=50 |

response:
```json
[
    {
      "id": 1,
      "created": "2024-10-26 18:39:50",
      "updated": "2024-10-26 18:39:50",
      "numzona": "SAMOC",
      "nomzona": "SAMUEL OCANA"
    }
    {}...
]
```
