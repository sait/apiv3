## Catalogo de Unidades

Rutas de unidades

| Accion                                          | Ruta                                |
|-------------------------------------------------|-------------------------------------|
| [Listado cataogo](#listar-catalogo-de-unidades) | GET  /api/v3/unidadescat?filters... |

Campos de unidades

| Campo    | Tipo     | Descripcion                                          |
|----------|----------|------------------------------------------------------|
| id       | int      | id incremental disponible solo atraves de SAIT APIv3 |
| created  | datetime | Timestamp del momento de creacion                    |
| updated  | datetime | Timestamp de ultima actualizacion                    |
| unidad   | string   | Clave de la unidad en SAIT                           |
| clavesat | string   | Clave de la unidad en el catalogo del SAT            |

### Listar catalogo de Unidades

GET /api/v3/unidadescat?filters...

| Variable | Descripcion                                              |
|----------|----------------------------------------------------------|
| offset   | A partir de que registro iniciar busqueda. Default 0     |
| limit    | Cuanto registros obtener. Default 100                    |
| order    | Orden deseado. Disponibles, updated, id, unidad,clavesat |
| q        | palabras a buscar (unidad o clavesat)                    |

```json
[
    {
      "id": 2,
      "created": "2024-06-29 12:20:34",
      "updated": "2024-06-29 12:20:34",
      "unidad": "PZA",
      "clavesat": "H87"
    },
    {}...
]
```