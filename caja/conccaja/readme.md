## Conceptos de caja

Rutas 

| Accion                                | Ruta                        |
|---------------------------------------|-----------------------------|
| [Listar conceptos](#listar-conceptos) | POST  /api/v3/conceptoscaja |

Campos de los Zonas

| Campo     | tipo          | Significado                                          |
|-----------|---------------|------------------------------------------------------|
| id        | int           | id incremental disponible solo através de SAIT API   |
| created   | datetime      | timestamp del momento de creación                    |
| updated   | datetime      | timestamp ultima actualizacion                       |
| tipodoc   | CHAR(5)       | Clave del concepto(tipo de pago)                     |
| desc      | VARCHAR(40)   | Descripción del concepto                             |
| conccxc   | CHAR(2)       | Movimiento genera en cobranza                        |
| tipomov   | CHAR(1)       | Tipo de movimiento (pago, entrada o salida)          |
| pidebenef | TINYINT       | Si requiere beneficiario                             |
| grupos    | VARCHAR(50)   | Grupos que autorizan este tipo de movimiento         |
| esmovefvo | TINYINT       | Si es movimiento de efectivo                         |
| piderefer | TINYINT       | Si requiere referencia                               |
| pjecom    | DECIMAL(10,5) | Porcentaje de comisión por aceptar este tipo de pago |
| mostrtot  | TINYINT       | Mostrar total al entregar corte                      |
| usaauto   | TINYINT(4)    | Si requiere autorización                             |
| gposauto  | VARCHAR(50)   | Grupos autorizados                                   |
| clavesat  | CHAR(2)       | Clave del sat                                        |

---
### Listar conceptos

GET /api/v3/conceptoscaja

| Variable | Significado                                           |
|----------|-------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0  |
| limit    | Cuantos registros obtener. Default 100                |
| order    | Orden deseado. Disponibles:updated,id,numzona,nomzona |
| q        | Palabras a buscar                                     |
| tipomov  | P:Pagos, S:Salidas, E:Entradas, etc                   |


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