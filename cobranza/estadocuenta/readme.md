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

GET api/v3/datosgenerales/828

response:
```json
{
    "numcli": "  828",
    "nomcli": "JAVIER ENRIQUEZ CURIEL",
    "telefono": "638-107-20-23",
    "rfc": "XAXX010101000",
    "cp": "83400",
    "pais": "MEXICO",
    "estado": "SONORA",
    "ciudad": "PUERTO PEÑASCO",
    "contacto": "",
    "emailcontacto": "",
    "saldomn": 31703.06,
    "limcred": 20000,
    "diascred": 8,
    "saldo": 0,
    "creditodisp": -11703.06,
    "totalcargos": 2931675.74,
    "totalabonos": 2900056.3,
    "sdocxcdls": 870.21,
    "sdocxc": 16543.22,
    "AdeudeMasAntiguo": {
        "fecha": "2026-06-04",
        "folio": " F   B106120",
        "saldo": "9084.45"
    }
}
```

---
### Listar movimientos

GET api/v3/cxc/mov?filters...

| Variable     | Significado                                                                                                                      |
|--------------|----------------------------------------------------------------------------------------------------------------------------------|
| offset       | A partir de que registro iniciar búsqueda. Default 0                                                                             |
| limit        | Cuantos registros obtener. Default 100                                                                                           |
| order        | Orden deseado. Disponibles: fecha, factura                                                                                       |
| numcli       | numero de identifiacion de cliente                                                                                               |
| fecha1       | traer movimientos con fecha mayor a fecha1                                                                                       |
| fecha2       | traer movimientos con fecha menor a fecha2                                                                                       |
| totalizar    | totalizar=true - mostrar totales de items y suma de importes segun cada divisa de movimientos                                    |
| format       | format=xlsx - generar reporte en XLSX, format=pdf - Generar reporte en PDF, format= - dejar vacio para obtener respuesta en json |
| soloconsaldo | solo mostrar movimientos que tienen saldo > 0                                                                                    |
| conc         | traer solo los movimientos de estado de cuenta cuando conc=?                                                                     |
| numdoc       | Obtener registro donde numdoc=?                                                                                                  |
| divisa       | Traer movimientos de estado de cuenta cunado divisa=?                                                                            |

response:
```json
[
    {
      "id": 52,
      "sec": 4,
      "keycxc": "       A2614",
      "fecha": "2022-09-30",
      "conc": "TS",
      "concdesc": "TRANSF. SANTANDER",
      "numdoc": "",
      "keyrefer": " F    A11363",
      "cargo": 0,
      "abono": 2043,
      "divisa": "P",
      "moneda": "MXN",
      "tc": 0,
      "saldoacum": -6932.48,
      "saldodoc": 0,
      "uuid": "1B5A40AB25DD4467A36F1987F9AC8C46"
    },
    {
      "id": 53,
      "sec": 5,
      "keycxc": "       A2615",
      "fecha": "2022-09-30",
      "conc": "TS",
      "concdesc": "TRANSF. SANTANDER",
      "numdoc": "",
      "keyrefer": " F    A11386",
      "cargo": 0,
      "abono": 425.5,
      "divisa": "P",
      "moneda": "MXN",
      "tc": 0,
      "saldoacum": -6932.48,
      "saldodoc": 0,
      "uuid": "1B5A40AB25DD4467A36F1987F9AC8C46"
    },
    {}...
]
```