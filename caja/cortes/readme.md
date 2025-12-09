## Modulo Caja

Rutas 

| Accion                          | Ruta                        |
|---------------------------------|-----------------------------|
| [Abrir Corte](#abrir-corte)     | POST  /api/v3/cortes        |
| [Cerrar Corte](#cerrar-corte)   | PUT   /api/v3/cortes        |
| [Leer Corte](#leer-corte)       | GET   /api/v3/cortes/:corte |
| [Listar Cortes](#listar-cortes) | GET   /api/v3/cortes        |

---
### Campos de Cortes2

| Campo      | tipo    | Significado                               |
|------------|---------|-------------------------------------------|
| id         | int     | Identificador incremental                 |
| created    | varchar | Fecha de creacion del registro en DB      |
| updated    | varchar | Fecha de actualizacion del registro en DB |
| corte      | varchar | Clave de identificacion del corte         |
| numalm     | varhcar | Numero de almacen                         |
| numest     | varchar | Numero de estacion (caja)                 |
| numuser    | varchar | Clave de usuario que abrio el corte       |
| fecha      | date    | Fecha de apertura de corte                |
| hora       | varchar | Hora de apertura de corte                 |
| numuserfin | varchar | Usuario que cerro el corte                |
| fechafin   | date    | Fecha de cierre del corte                 |
| horafin    | varchar | Hora de cierre del corte                  |
| numusercc  | varchar | Clave del usuario que entrego             |
| fechacc    | date    | Fecha de entrega                          |
| horacc     | varchar | Hora de entrega                           |
| numop      | decimal | Numero de operaciones realizadas          |
| entpagos   | decimal | Cantidad de dinero que entro como pago    |
| salcorte   | decimal | Cantidad de dinero que salio del corte    |
| entotrmov  | decimal | Entrada por otro movimiento               |
| salotrmov  | decimal | Salida por otro movimiento                |
| efectivo   | decimal | Efectivo total del corte                  |
| tc         | decimal | Tipo de cambio                            |
| modificado | tinyint | Si fue modificado el corte                |

---
### Abrir corte

POST /api/v3/cortes

request:
```json
{
    "numalm": "1",
    "numest": "1",
    "fondo": {
        "mxn": {
            "total":1888.50,
            "1": 1,
            "2": 1,
            "5": 1,
            "10": 1,
            "20": 1,
            "50": 1,
            "100": 1,
            "200": 1,
            "500": 1,
            "1000": 1,
            ".50": 1
        },
        "dlls": {
            "total":188.41,
            "1": 1,
            "2": 1,
            "5": 1,
            "10": 1,
            "20": 1,
            "50": 1,
            "100": 1,
            ".25": 1,
            ".10": 1,
            ".05": 1,
            ".01": 1
        }
    }
}
```

response:
```json
{
    "id": "36",
    "created": "2025-04-25 23:17:48",
    "updated": "2025-04-25 23:17:48",
    "corte": "    W14",
    "numalm": " 1",
    "numuser": "  MSL",
    "fecha": "2025-04-25",
    "string": "",
    "numest": "1",
    "numuserfin": "",
    "fechafin": "",
    "horafin": "",
    "numusercc": "",
    "fechacc": "",
    "horacc": "",
    "numop": 1,
    "entpagos": 0,
    "salcorte": 0,
    "entotrmov": 1500,
    "salotrmov": 0,
    "efectivo": 1500,
    "tc": 20.21
}
```

---
### Cerrar corte

PUT /api/v3/cortes

 * los campos numalm y numest son **obligatorios**
 
 ```json
 {
    "numalm" : "1",
    "numest" : "1"
 }
 ```

response:
```json
"Corte W16 cerrado"
```

---
### Leer corte

GET /api/v3/cortes/:corte

response:
```json
{
    "id": "41",
    "created": "2025-05-02 21:14:28",
    "updated": "2025-05-02 21:16:43",
    "corte": "    W33",
    "numalm": " 1",
    "numuser": "  MSL",
    "fecha": "2025-05-02",
    "hora": "14:14:28",
    "numest": "1",
    "numuserfin": "  MSL",
    "fechafin": "2025-05-02",
    "horafin": "14:16:43",
    "numusercc": "",
    "fechacc": "",
    "horacc": "",
    "numop": 1,
    "entpagos": 0,
    "salcorte": 0,
    "entotrmov": 3807.77,
    "salotrmov": 0,
    "efectivo": 3807.77,
    "tc": 20.21,
    "Movimientos": [
        {
            "id": "121",
            "created": "2025-05-02 21:15:18",
            "updated": "2025-05-02 21:15:18",
            "corte": "    W33",
            "numdoc": "    1",
            "tipodoc": "FONDO",
            "tipomov": "E",
            "es": "",
            "numuser": "  MSL",
            "numalm": " 1",
            "fecha": "2025-05-02",
            "hora": "14:14:28",
            "numcli": "     ",
            "numbenef": "",
            "importe": 188.41,
            "pago": 0,
            "divisa": "D",
            "tc": 20.21,
            "cancelado": 0,
            "keydocum": "",
            "refer": "",
            "desglose": "100 = 1\n50 = 1\n20 = 1\n10 = 1\n5 = 1\n2 = 1\n1 = 1\n.25 = 1\n.10 = 1\n.05 = 1\n.01 = 1\n"
        }
    ]
}
```

---
### Listar cortes

GET /api/v3/cortes

| Variable | Tipo de filtro         | Significado                                                         |
|----------|------------------------|---------------------------------------------------------------------|
| offset   |                        | A partir de que registro iniciar búsqueda. Default 0                |
| limit    |                        | Cuantos registros obtener. Default 100                              |
| order    |                        | Orden deseado. Disponibles:updated,id,numzona,nomzona               |
| q        | Busqueda por similitud | corte, numuser                                                      |
| fecha1   | busqueda mayor a       | traer cortes con fecha de registro mayor a "2025-03-21"             |
| fecha2   | busqueda menor a       | traer cortes con fecha de registro menor a "2025-03-29"             |
| corte1   | busqueda mayor a       | traer cortes con folio de registro mayor a "      A"                |
| corte2   | busqueda menor a       | traer cortes con fecha de registro menor a "Z      "                |
| numalm   | busqueda por exactitud | traer cortes con numero de sucursal/almacen igual a " 2"            |
| numuser  | busqueda por exactitud | traer cortes con clave de usuario igual = "  MSL"                   |
| numest   | busqueda por exactitud | traer cortes con numero de caja igual = "1"                         |
| status   | busqueda por exactitud | traer cortes con estatus igual a A=Abierto, E=Entregado o C=Cerrado |

response:
```json
[
    {
        "corte": "    W33",
        "fecha": "2025-05-02",
        "caja": "1",
        "usuario": "SAIT NACIONAL",
        "hora_inicial": "14:14:28",
        "hora_final": "14:16:43",
        "status": "Entregado"
    },
    {
        "corte": "    W34",
        "fecha": "2025-05-02",
        "caja": "1",
        "usuario": "SAIT NACIONAL",
        "hora_inicial": "14:25:06",
        "hora_final": "14:30:14",
        "status": "Entregado"
    },
]
```