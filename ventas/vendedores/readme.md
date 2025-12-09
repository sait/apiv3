## Vendedores

Rutas de Vendedores

| Accion                             | Ruta                                   |
|------------------------------------|----------------------------------------|
| [Crear](#crear-vendedores)         | POST   /api/v3/vendedores              |
| [Leer](#leer-vendedor)             | GET    /api/v3/vendedores/:numvend     |
| [Actualizar](#actualizar-vendedor) | PUT    /api/v3/vendedores/:numvend     |
| [Borrar](#borrar-vendedor)         | DELETE /api/v3/vendedores/:numvend     |
| [Buscar](#buscar-vendedores)       | GET    /api/v3/vendedores?variables... |



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
### Crear vendedores

POST /api/v3/vendedores

request:
```json
{
  "numvend"    : "DEV1",
	"nomvend"    : "Luis vendedor",
	"comision"   : 5.00,
	"maxpjedesc" : 30.00,
	"clasif1"    : "",
	"plista"     : 0,
	"email"      : "pruebavend@gmail.com"
}
```

response:
```json
{
  "id"         : 143,
  "created"    : "2024-10-30 19:21:28",
  "updated"    : "2024-10-30 19:21:28",
  "numvend"    : " DEV1",
  "nomvend"    : "Luis vendedor",
  "comision"   : 5,
  "maxpjedesc" : 30,
  "clasif1"    : "",
  "plista"     : 0,
  "email"      : "pruebavend@gmail.com"
}
```

---
### Leer vendedor

GET /api/v3/vendedores/DEV1

response:
```json
{
  "id"         : 143,
  "created"    : "2024-10-30 19:21:28",
  "updated"    : "2024-10-30 19:21:28",
  "numvend"    : " DEV1",
  "nomvend"    : "Luis vendedor",
  "comision"   : 5,
  "maxpjedesc" : 30,
  "clasif1"    : "",
  "plista"     : 0,
  "email"      : "pruebavend@gmail.com"
}
```

---
### Actualizar vendedor

PUT /api/v3/vendedores/DEV1

request:
```json
{
	"nomvend"  : "Luis vendedor",
  "comision"   : 5,
  "maxpjedesc" : 30,
  "clasif1"    : "",
  "plista"     : 0,
	"email"    : "pruebavend2@gmail.com"
}
```

response:
```json
"UPDATED"
```

---
### Borrar vendedor

DELETE /api/v3/vendedores/DEV1
response:
```json
"DELETED"
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

| Ejemplo de Búsqueda                | Ruta                         |
|------------------------------------|------------------------------|
| Limitar campos                     | /api/v3/vendedores?limit=100 |
| indicar desde que registro empezar | /api/v3/vendedores?offset=50 |

response:
```json
[
    {
      "id": 138,
      "created": "2024-10-16 21:30:14",
      "updated": "2024-10-16 21:30:14",
      "numvend": "    1",
      "nomvend": "Juan Perez",
      "comision": 0,
      "maxpjedesc": 0,
      "clasif1": "",
      "plista": 0,
      "email": ""
    },
    {
      "id": 139,
      "created": "2024-10-16 21:30:14",
      "updated": "2024-10-16 21:30:14",
      "numvend": "    2",
      "nomvend": "José Mateo",
      "comision": 0,
      "maxpjedesc": 0,
      "clasif1": "",
      "plista": 0,
      "email": ""
    },
    {}...
]
```
