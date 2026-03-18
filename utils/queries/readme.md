## Queries

Permite la definición de comados select de SQL para ejecutar busquedas en SAITNube, desde SAITErp


| Accion                                          | Ruta                                                          |
|-------------------------------------------------|---------------------------------------------------------------|
| Crear                                           | POST /api/v3/queries                                          |
| Actualizar                                      | PUT  /api/v3/queries/:id                                      |
| Leer                                            | GET  /api/v3/queries/:id                                      |
| Borrar                                          | GET  /api/v3/queries/:id                                      |
| [Buscar consultas](#buscar-consultas)           | GET  /api/v3/consultas?filtros...                             |
| [Leer consulta](#leer-consulta-por-descripcion) | GET  /api/v3/consultas/:descrip                               |
| [Ejecutar consulta](#ejecutar-consulta)         | GET  /api/v3/consultas/exec/:uid?varname1=val&varname2=val... |

Campos de los queries

| Campo     | Significado                                    |
|-----------|------------------------------------------------|
| id        | incremental                                    |
| created   | fecha de creacion                              |
| modified  | fecha de ultima modificacion                   |
| uid       | unique id que programador colocara en SAIT.exe |
| descrip   | descripcion de lo que hace el query            |
| usuarios  | usuarios que pueden ejecutar el query          |
| grupos    | grupos que pueden ejecutar el query            |
| modulo    | modulo relacionado al query                    |
| filtros   | filtros considerados para editar en frontend   |
| cmdselect | comando select sql a ejecutar                  |


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

---
### Buscar consultas

GET /api/v3/consultas?filters...

| Filtros | significado                                | Tipo de filtro         | Uso                                                                  |
|---------|--------------------------------------------|------------------------|----------------------------------------------------------------------|
| offset  | A partir del registro a iniciar. Default 0 | despues de             | desde que registro empezar: offset=10                                |
| limit   | Cuantos registros obtener. Default 100     | cantidad               | cuantos registros recibir: limit=50                                  |
| q       | Palabras a buscar (descrip,modulo)         | busqueda por similitud | traer queries donde el modulo o descrip similar a q="Ventas"         |
| modulo  | clasificacion del query                    | busqueda por exactitud | traer facturas donde numdoc,numcli,nomcliev sean similares a q=AF134 |

response:
```json
[
  {
    "id": 11,
    "descrip": "Ventas 1. Ventas y costo por artículo",
    "modulo": "ventas",
  },
  {
    "id": 12,
    "descrip": "Ventas 3. Historia de ventas de artículo",
    "modulo": "ventas"
  }
]
```

---
### Leer consulta por descripcion

GET /api/v3/consultas/:descrip

```
Ejemplo: "/api/v3/consultas/Ventas%201.%20Ventas%20y%20costo%20por%20artículo"
```

response:
```json
{
    "id": 11,
    "uid": "ventas1",
    "descrip": "Ventas 1. Ventas y costo por artículo",
    "modulo": "ventas",
    "filtros": [
        {
            "name": "vfecha1",
            "label": "Fecha inicial",
            "type": "fecha",
            "required": true,
            "seloptions": null
        },
        {
            "name": "vfecha2",
            "label": "Fecha inicial",
            "type": "fecha",
            "required": true,
            "seloptions": null
        },
        {
            "name": "vnumdoc1",
            "label": "Documento inicial",
            "type": "text",
            "required": true,
            "seloptions": null
        },
        {
            "name": "vnumdoc2",
            "label": "Documento final",
            "type": "text",
            "required": true,
            "seloptions": null
        },
        {
            "name": "vnumart1",
            "label": "Articulo inicial",
            "type": "text",
            "required": true,
            "seloptions": null
        },
        {
            "name": "vnumart2",
            "label": "Articulo final final",
            "type": "text",
            "required": true,
            "seloptions": null
        },
        {
            "name": "vnumvend",
            "label": "Vendedor",
            "type": "vendedor",
            "required": false,
            "seloptions": null
        },
        {
            "name": "vnumcli",
            "label": "Cliente",
            "type": "cliente",
            "required": false,
            "seloptions": null
        },
        {
            "name": "vnumalm",
            "label": "Sucursal",
            "type": "sucursal",
            "required": false,
            "seloptions": null
        },
        {
            "name": "vnumfam",
            "label": "Famlia de articulo",
            "type": "text",
            "required": false,
            "seloptions": null
        },
        {
            "name": "vnumlin",
            "label": "Linea de articulo",
            "type": "text",
            "required": false,
            "seloptions": null
        }
    ]
}
```

---
### Ejecutar consulta

GET /api/v3/consultas/exec/:uid?filters...

```
ejemplo: /api/v3/consultas/exec/ventas1?vfecha1=20240101&vfecha2=20240101&vnumcli=&vnumvend=&vnumalm=&vnumlin=&vnumfam&vnumdoc1=&vnumdoc2=&vnumart1=&vnumart2=
```

response:
```json
[
    {
        "cantidad": "1.00",
        "clave": "1935021",
        "costo": "0.00",
        "costopro": "0.00",
        "descuento": "0.00",
        "devol": "0.00",
        "importe": "573.15",
        "iva": "45.85",
        "nombre": "LAPIZ PRISMACOLOR PREMIER C/24",
        "subtotal": "573.15",
        "total": "619.00",
        "ultcosto": "0.00"
    },
    {
        "cantidad": "2.00",
        "clave": "019205",
        "costo": "0.00",
        "costopro": "0.00",
        "descuento": "0.00",
        "devol": "0.00",
        "importe": "287.04",
        "iva": "22.96",
        "nombre": "(ESPECIAL) JUEGO DE GEOMETRIA MAPED IRRO",
        "subtotal": "287.04",
        "total": "310.00",
        "ultcosto": "0.00"
    },
    {
        "cantidad": "1.00",
        "clave": "LAP-NINA",
        "costo": "0.00",
        "costopro": "0.00",
        "descuento": "0.00",
        "devol": "0.00",
        "importe": "230.56",
        "iva": "18.44",
        "nombre": "LAPICERA RIGIDA NIÑA CONEJITO",
        "subtotal": "230.56",
        "total": "249.00",
        "ultcosto": "0.00"
    }
]
```