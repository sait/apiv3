## Beneficiarios en caja

Rutas

| Accion                                        | Ruta                                  |
|-----------------------------------------------|---------------------------------------|
| [Listar beneficiarios](#listar-beneficiarios) | GET  /api/v3/beneficiarios?filters... |

Campos de los Zonas

| Campo    | tipo        | Significado                                        |
|----------|-------------|----------------------------------------------------|
| id       | int         | id incremental disponible solo através de SAIT API |
| created  | datetime    | timestamp del momento de creación                  |
| updated  | datetime    | timestamp ultima actualizacion                     |
| numbenef | VARCHAR(6)  | Calve del beneficiario                             |
| nombenef | VARCHAR(60) | Nombre del beneficiario                            |
| rfc      | VARCHAR(13) | RFC del beneficiario                               |

---
### Listar beneficiarios

GET /api/v3/beneficiarios

| Variable | Tipo de filtro         | Significado                                                                   |
|----------|------------------------|-------------------------------------------------------------------------------|
| offset   | offset                 | A partir de que registro iniciar búsqueda. Default 0                          |
| limit    | limit                  | Cuantos registros obtener. Default 100                                        |
| order    | ordenar por            | Orden deseado. Disponibles:updated,id,numzona,nomzona                         |
| q        | busqueda similar a     | Palabras a buscar (corte, nombre de cliente, descripcion de concepto de caja) |
| numbenef | busqueda por exactitud | Numero de identificacion de beneficiario                                      |

```json
[
]
```