## Vendedores

Campos de los Vendedores

| Campo      | tipo     | Significado                                              |
|------------|----------|----------------------------------------------------------|
| id         | int      | id incremental disponible solo através de SAIT API       |
| created    | datetime | timestamp del momento de creación                        |
| updated    | datetime | timestamp ultima actualizacion                           |
| numvend    | varchar  | Identificador del vendedor                               |
| nomvend    | varchar  | Nombre del vendedor                                      |
| comision   | decimal  | % de Comisión del vendedor                               |
| maxpjedesc | decimal  | Maximo porcentaje que puede aplicar un vendedor          |
| clasif1    | varchar  | Clasificación del vendedor.                              |
| plista     | decimal  | Precio Minimo Permitido a otorgar a los clientes (0 a 5) |
| email      | varchar  | Correo electronico del vendedor                          |

---
### Leer vendedor

GET /api/v3/vendedores/DEV1

response:
```json
{
    "id": 0,
    "created": "",
    "updated": "",
    "numvend": " TEST",
    "nomvend": "MSL TEST",
    "comision": 0,
    "maxpjedesc": 10,
    "clasif1": "",
    "plista": 3,
    "email": ""
}
```

---
### Buscar Vendedores

GET /api/v3/vendedores?variables

| Variable | Significado                                           |
|----------|-------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0  |
| limit    | Cuantos registros obtener. Default 100                |
| order    | Orden deseado. Disponibles:updated,id,numvend,nomvend |
| q        | Palabras a buscar                                     |

response:
```json
[
    {
        "id": 0,
        "created": "",
        "updated": "",
        "numvend": "  MSL",
        "nomvend": "SAIT NACIONAL",
        "comision": 0,
        "maxpjedesc": 0,
        "clasif1": "",
        "plista": 0,
        "email": ""
    },
    {
        "id": 0,
        "created": "",
        "updated": "",
        "numvend": " TEST",
        "nomvend": "MSL TEST",
        "comision": 0,
        "maxpjedesc": 10,
        "clasif1": "",
        "plista": 3,
        "email": ""
    }
    {}...
]
```