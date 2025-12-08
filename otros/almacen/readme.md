Campos de los Almacenes

Rutas de Almacenes

| Accion                            | Ruta                                    |
|-----------------------------------|-----------------------------------------|
| [Leer](#leer-almacen)             | GET    /api/v3/almacenes/:numalm        |
| [Buscar](#buscar-almacenes)       | GET    /api/v3/almacenes?condiciones... |

| Campo    | Significado                                        |
|----------|----------------------------------------------------|
| id       | id incremental disponible solo através de SAIT API |
| created  | timestamp del momento de creación                  |
| updated  | timestamp ultima actualizacion                     |
| numalm   | Numero de identificacion almacen                   |
| nomalm   | Nombre de almacen                                  |
| calle    | Direccion del almacen                              |
| numext   | Numero exterior                                    |
| colonia  | Colonia                                            |
| ciudad   | Ciudad                                             |
| estado   | Estado                                             |
| cp       | Codigo postal                                      |
| seriedoc | Serie a utilizar al realizar documentos            |

---
## Leer almacen

GET /api/v3/almacenes/1

response:
```json
{
    "id": 1,
    "created": "2024-06-29 12:37:37",
    "updated": "2025-12-05 11:16:49",
    "numalm": " 1",
    "nomalm": "PRISMA TECATE",
    "niveles": "SUPER,CT,G,J,LDT",
    "ultid": "",
    "ultss": "        A1",
    "salxcapa": 0,
    "obligacad": 0,
    "obligalot": 0,
    "numalmprim": "",
    "calle": "AV. HIDALGO",
    "numext": "183 OTE",
    "colonia": "CENTRO",
    "ciudad": "TECATE",
    "Estado": "",
    "cp": "21400",
    "seriedoc": "AT"
},
```

---
### Buscar Almacenes

GET /api/v3/almacenes?filtros

| Filtros | Significado                                          |
|---------|------------------------------------------------------|
| offset  | A partir de que registro iniciar búsqueda. Default 0 |
| limit   | Cuantos registros obtener. Default 100               |
| order   | Orden deseado. Disponibles: updated,id,numalm,nomalm |
| q       | Palabras a buscar (numalm, nomalm, niveles, cp)      |
| estado  | filtro por estado del pais                           |
| ciudad  | ciudad donde se ubica la sucursal/almacen            |

response:
```json
[
    {
        "id": 1,
        "created": "2024-06-29 12:37:37",
        "updated": "2025-12-05 11:16:49",
        "numalm": " 1",
        "nomalm": "PRISMA TECATE",
        "niveles": "SUPER,CT,G,J,LDT",
        "ultid": "",
        "ultss": "        A1",
        "salxcapa": 0,
        "obligacad": 0,
        "obligalot": 0,
        "numalmprim": "",
        "calle": "AV. HIDALGO",
        "numext": "183 OTE",
        "colonia": "CENTRO",
        "ciudad": "TECATE",
        "Estado": "",
        "cp": "21400",
        "seriedoc": "AT"
    },
    {
        "id": 2,
        "created": "2024-06-29 12:37:37",
        "updated": "2025-01-31 15:51:46",
        "numalm": " 2",
        "nomalm": "PAPELIA ENCINOS",
        "niveles": "SUPER,CT,G,J,LDT",
        "ultid": "",
        "ultss": "        B1",
        "salxcapa": 0,
        "obligacad": 0,
        "obligalot": 0,
        "numalmprim": "",
        "calle": "BLVD. NUEVO LEON",
        "numext": "17-18",
        "colonia": "PLAZA ENCINOS",
        "ciudad": "TECATE",
        "Estado": "",
        "cp": "21460",
        "seriedoc": ""
    },
    {}...
]
```