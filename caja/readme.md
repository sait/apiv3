## Movimientos en caja

Rutas

| Accion                                    | Ruta                         |
|-------------------------------------------|------------------------------|
| [Listar movimientos](#listar-movimientos) | GET  /api/v3/caja?filters... |

Campos de los Zonas

| Campo     | tipo          | Significado                                                                        |
|-----------|---------------|------------------------------------------------------------------------------------|
| id        | int           | id incremental disponible solo através de SAIT API                                 |
| created   | datetime      | timestamp del momento de creación                                                  |
| updated   | datetime      | timestamp ultima actualizacion                                                     |
| corte     | VARCHAR(7)    | Número de corte                                                                    |
| numdoc    | CHAR(5)       | Número de documento del corte                                                      |
| tipodoc   | CHAR(5)       | Tipo del documento (Fondo, Efectivo, tarjeta, cheque)                              |
| keydocum  | VARCHAR(12)   | Índice de la tabla Docum donde se almacena información de la nota tiponum + numdoc |
| tipomov   | CHAR(1)       | Tipo del movimiento (entrada o pago)                                               |
| numuser   | CHAR(5)       | Clave del usuario                                                                  |
| numalm    | CHAR(2)       | Clave del almacén                                                                  |
| fecha     | DATE          | Fecha del movimiento                                                               |
| hora      | VARCHAR(8)    | Hora del movimiento                                                                |
| numcli    | CHAR(5)       | Clave del cliente                                                                  |
| numbenef  | VARCHAR(6)    | Clave del beneficiario                                                             |
| importe   | DECIMAL(12,2) | Importe total                                                                      |
| pago      | DECIMAL(12,2) | Pago del cliente                                                                   |
| divisa    | VARCHAR(1)    | Divisa                                                                             |
| tc        | DECIMAL(8,4)  | Tipo de cambio                                                                     |
| cancelado | TINYINT       | Si esta cancelado                                                                  |
| refer     | VARCHAR(20)   | Referencia en casa de ser cheque u otro método que lo necesite                     |
| desgloce  | TEXT          | Desglose de divisas por denominación                                               |

---
### Listar movimientos

GET /api/v3/caja

| Variable | Tipo de filtro         | Significado                                                                   |
|----------|------------------------|-------------------------------------------------------------------------------|
| offset   | offset                 | A partir de que registro iniciar búsqueda. Default 0                          |
| limit    | limit                  | Cuantos registros obtener. Default 100                                        |
| order    | ordenar por            | Orden deseado. Disponibles:updated,id,numzona,nomzona                         |
| q        | busqueda similar a     | Palabras a buscar (corte, nombre de cliente, descripcion de concepto de caja) |
| fecha1   | busqueda mayor a       | traer movimientos de caja con fecha de registro mayor a "2025-03-21"          |
| fecha2   | busqueda menor a       | traer movimientos de caja con fecha de registro menor a "2025-03-29"          |
| folio1   | busqueda mayor a       | traer movimientos de caja con folio mayor a "A"                               |
| folio2   | busqueda menor a       | traer movimientos de caja con folio menor a "Z"                               |
| corte1   | busqueda mayor a       | traer movimientos de caja con corte mayor a "A"                               |
| corte2   | busqueda menor a       | traer movimientos de caja con corte menor a "Z"                               |
| numalm   | busqueda por exactitud | numero de sucursal/almacen                                                    |
| tipo     | busqueda por exactitud | tipo de movimiento                                                            |
| numcli   | busqueda por exactitud | numero de identificacion de cliente                                           |
| numbenef | busqueda por exactitud | numero de identificacion de beneficiario                                      |

```json
[
    {
      "tipodoc": "   CH",
      "desc": "CHEQUE",
      "conccxc": "CH",
      "tipomov": "P",
      "pidebenef": 0,
      "pjecom": 0,
      "grupos": "CAJ,GVENT,CONT,SUPER",
      "esmovefvo": 0,
      "piderefer": 1,
      "mostrtot": 1,
      "usaauto": 0,
      "gposauto": "",
      "clavesat": "02",
      "planpagos": 0,
      "provpago": 0
    },
    {
      "tipodoc": "   EF",
      "desc": "EFECTIVO",
      "conccxc": "EF",
      "tipomov": "P",
      "pidebenef": 0,
      "pjecom": 0,
      "grupos": "CAJ,GVENT,CONT,SUPER",
      "esmovefvo": 1,
      "piderefer": 0,
      "mostrtot": 0,
      "usaauto": 0,
      "gposauto": "",
      "clavesat": "01",
      "planpagos": 0,
      "provpago": 0
    }
]
```