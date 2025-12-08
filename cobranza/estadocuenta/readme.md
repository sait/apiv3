## Estado de cuenta del cliente

Rutas de estado de cuenta

| Accion                                                      | Ruta                                  |
|-------------------------------------------------------------|---------------------------------------|
| [Datos generales del cliente](#datos-generales-del-cliente) | GET api/v3/cxc/datosgenerales/:numcli |
| [Listar movimientos](#listar-movimientos)                   | GET api/v3/cxc/mov?filters            |

---
**Campos generales de estado de cuenta del cliente**

| Campo       | Tipo   | Significado                                   |
|-------------|--------|-----------------------------------------------|
| numcli      | string | Numero de identificacion del cliente          |
| nomcli      | string | Nombre del cliente                            |
| saldomn     | float  | Saldo total utilizado                         |
| limcred     | float  | Limite disponible del cliente                 |
| totalcargos | float  | Total de los cargos realizados por el cliente |
| totalabonos | float  | Total de los abonos realizados por el cliente |
| sdocxcdls   | float  | Saldo total utilizado en dolares              |
| sdocxc      | float  | Saldo total utilizado en pesos                |

---
**Campos de movimientos registrados en cuenta de cliente**

| Campo        | Tipo   | Significado                                  |
|--------------|--------|----------------------------------------------|
| fecha        | string | fecha de creacion del documento              |
| keycxc       | string | clave unica de identificacion de documento   |
| concdesc     | string | descripcion de concepto del movimiento       |
| numdoc       | string | numero de identificacion de documento        |
| refer        | string | referencia del documento                     |
| cargo        | float  | Cargos aplicados a la cuenta del cliente     |
| abono        | float  | Abonos realizados al cargo                   |
| divisa       | string | divisa del movimiento                        |
| moneda       | string | Moneda segun divisa de documento             |
| tc           | float  | Tipo de cambio de divisa                     |
| saldoacummxn | float  | saldo acumulado en total en pesos            |
| saldodoc     | float  | saldo individual de documento                |
| importe      | float  | importe realizado por                        |
| uuid         | string | llave unica de factura o comprobante de pago |

---
### Datos generales del cliente

GET api/v3/datosgenerales/1144

response:
```json
{
    "numcli" : " 1144",
    "nomcli" : "ARROYO HOLDINGS DE MEXICO",
    "saldomn" : 7556.17,
    "limcred" : 40000,
    "totalcargos" : 866206.1,
    "totalabonos" : 858649.93,
    "sdocxcdls" : 0,
    "sdocxc" : 7556.17
}
```

---
### Listar movimientos

GET api/v3/cxc/mov?filters...

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,numlin         |
| numcli   | numero de identifiacion de cliente                   |

response:
```json
[
        {
        "keycxc": " F    A17196",
        "fecha": "2024-03-14",
        "conc": "FA",
        "concdesc": "FACTURA",
        "numdoc": "A17196",
        "refer": "FAA17196",
        "cargo": 0,
        "abono": 1755.65,
        "divisa": "P",
        "moneda": "mxn",
        "tc": 0,
        "saldoacummxn": 0,
        "saldodoc": 0,
        "uuid": ""
    },
    {
        "keycxc": "       A4797",
        "fecha": "2024-03-22",
        "conc": "TS",
        "concdesc": "TRANSF. SANTANDER",
        "numdoc": "",
        "refer": "FAA17196",
        "cargo": 0,
        "abono": 1755.65,
        "divisa": "P",
        "moneda": "mxn",
        "tc": 0,
        "saldoacummxn": 1755.65,
        "saldodoc": 0,
        "uuid": "E7C2802ED9124EFF999A7A4D0168574E"
    }
    {}...
]
```