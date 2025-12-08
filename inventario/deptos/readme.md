## Departamentos de articulos

Rutas de departamentos

| Acción                          | Ruta                            |
|---------------------------------|---------------------------------|
| [Leer](#leer-departamento)      | GET   /api/v3/deptos/:numfam    |
| [Listar](#listar-departamentos) | GET   /api/v3/deptos?filters... |

Campos de departamentos

| Campo   | Tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | Id incremental disponible solo atraves de SAIT API |
| created | datetime | Timestamp del momentode creacion                   |
| updated | datetime | Timestamp ultima actualizacion                     |
| valdep  | string   | Numero del departamento                            |
| numdep  | string   | Numero de departamento                             |
| nomdep  | string   | Descripcion del departamento                       |

---
### Leer

GET /api/v3/deptos/:numdep

response
```json
{
    "valdep": "  1",
    "numdep": "1",
    "nomdep": "NATURAL"
}
```

---
### Listar

GET /api/v3/deptos?filters

| Variable | Significado                                                              |
|----------|--------------------------------------------------------------------------|
| offset   | A partir de que registro iniciar busqueda. Default: 0                    |
| limit    | Cuantos registros obtener. Default: 100                                  |
| order    | Orden deseado. Disponibles: id, updated, created, valdep, numdep, nomdep |
| q        | Palabras a buscar (nomdep, numdep)                                       |

```json
[
    {
      "valdep": "  1",
      "numdep": "1",
      "nomdep": "NATURAL"
    },
    {
      "valdep": "  2",
      "numdep": "2",
      "nomdep": "COLLINS LABORATORIO"
    },
    {
      "valdep": " 50",
      "numdep": "50",
      "nomdep": "NUTRISSE OFERTA"
    },
    {}...
]
```