## Queries

Permite la definición de comados select de SQL para ejecutar busquedas en SAITNube, desde SAITErp


| Accion     | Ruta                                                        |
| ---------- | ----------------------------------------------------------- |
| Crear      | POST /api/v3/queries                                        |
| Actualizar | PUT  /api/v3/queries/:id                                    |
| Leer       | GET  /api/v3/queries/:id                                    |
| Borrar     | GET  /api/v3/queries/:id                                    |
| Ejecutar   | GET  /api/v3/queries/exec/:uid?varname1=val&varname2=val... |


Campos de los queries

| Campo      | Significado                                                |
| ---------- | ---------------------------------------------------------- |
| id         | incremental                                                |
| created    | fecha de creacion                                          |
| modified   | fecha de ultima modificacion                               |
| uid        | unique id que programador colocara en SAIT.exe             |
| descrip    | descripcion de lo que hace el query                        |
| usuarios   | usuarios que pueden ejecutar el query, en formato csv      |
| grupos     | grupos que pueden ejecutar el query, en formato csv        |
| cmdselect  | comando select sql a ejecutar                              |


---
### Crear Queries

POST /api/v3/queries

request:
```json
{
    "descrip"   : "Obtener todos los clientes eventuales",
    "usuarios"  : "MSL",
    "grupos"    : "*",
    "cmdselect" : "SELECT * FROM clievent"
}
```

response:
```json
"CREATED"
```

---
### Leer Query por Id

GET /api/v3/queries/:id

response:
```json
{
    "id"        : 1, 
    "created"   : "2023-02-07 21:37:46",
    "updated"   : "2023-02-07 21:40:08",
    "descrip"   : "Obtener todos los clientes eventuales",
    "usuarios"  : "MSL",
    "grupos"    : "*",
    "cmdselect" : "SELECT * FROM clievent"
}
```

---
### Actualizar Query

PUT /api/v3/queries/:id

request:
```json
{
    "descrip"   : "Obtener todos los clientes eventuales",
    "usuarios"  : "MSL",
    "grupos"    : "*",
    "cmdselect" : "SELECT * FROM clievent"
}
```

response:
```json
"UPDATED"
```

---
### Eliminar Query

DELETE /api/v3/queries/:id

response:
```json
"DELETED"
```