## Almacenes

Rutas de Almacenes

| Accion                            | Ruta                                    |
|-----------------------------------|-----------------------------------------|
| [Crear](#crear-almacen)           | POST   /api/v3/almacenes                |
| [Leer](#leer-almacen)             | GET    /api/v3/almacenes/:numalm        |
| [Actualizar](#actualizar-almacen) | PUT    /api/v3/almacenes/:numalm        |
| [Borrar](#borrar-almacen)         | DELETE /api/v3/almacenes/:numalm        |
| [Buscar](#buscar-almacenes)       | GET    /api/v3/almacenes?condiciones... |

Campos de los Almacenes

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

### Crear almacen

POST /api/v3/almacenes

request:
```json
{
  "numalm"   : "39",
  "nomalm"   : "Almacen OTO",
  "calle"    : "Palo fierro",
  "numext"   : "2",
  "colonia"  : "Samuel Ocana",
  "ciudad"   : "San Luis R.C.",
  "estado"   : "Sonora",
  "cp"       : "83459",
  "seriedoc" : "T"
}
```

response:
```json
{
  "calle"    : "Palo fierro",
  "ciudad"   : "San Luis R.C.",
  "colonia"  : "Samuel Ocana",
  "cp"       : "83459",
  "estado"   : "Sonora",
  "nomalm"   : "Almacen OTO",
  "numalm"   : "39",
  "numext"   : "2",
  "seriedoc" : "T"
}
```

---

### Leer almacen

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
}
```

---

### Actualizar almacen

PUT /api/v3/almacenes/39

request:
```json
{
	"nomalm"   : "Almacen 2 OTO",
	"calle"    : "Palo fierro",
	"numext"   : "2",
	"colonia"  : "Samuel Ocana",
	"ciudad"   : "San Luis R.C.",
	"estado"   : "Sonora",
	"cp"       : "83459",
	"seriedoc" : "T"
}
```

response:
```json
{
  "calle"    : "Palo fierro",
  "ciudad"   : "San Luis R.C.",
  "colonia"  : "Samuel Ocana",
  "cp"       : "83459",
  "estado"   : "Sonora",
  "nomalm"   : "Almacen 2 OTO",
  "numalm"   : "39",
  "numext"   : "2",
  "seriedoc" : "T"
}
```

---

### Borrar almacen

DELETE /api/v3/almacenes/39

response:
```json
"DELETED"
```

---

### Buscar Almacenes

GET /api/v3/almacenes?variables

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles: updated,id,numalm,nomalm |
| q        | Palabras a buscar (numalm, nomalm, niveles, cp)      |
| estado   | filtro por estado del pais                           |

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
