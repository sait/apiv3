## Facturas

Rutas de facturas

| Accion                            | Ruta                           |
|-----------------------------------|--------------------------------|
| [Leer por clave](#leer-por-clave) | GET   /api/v3/facturas/:numdoc |

---
### Leer por Clave

GET /api/v3/facturas/:numdoc

response:
```json
{
"id": 2005588,
    "created": "2024-10-09 23:58:02",
    "updated": "2024-10-09 23:58:02",
    "tipodoc": " F",
    "numdoc": "    AF4858",
    "fecha": "2024-10-09",
    "hora": "16:58:02",
    "numcli": "    0",
    "numcliev": "      A-10",
    "numalm": " 2",
    "mostrador": "",
    "divisa": "P",
    "tc": 16.35,
    "importe": 14.81,
    "pjedesc": 0,
    "descuento": 0,
    "impuesto1": 1.19,
    "impuesto2": 0,
    "costo": 8.789999961853027,
    "costo2": 0,
    "costopro": 0,
    "total": 16,
    "obs": "Nota facturada: B744013",
    "items": [
      {
        "cant": 2,
        "numart": "              IMPACC",
        "unidad": "PZA",
        "precio": 7.40741,
        "impuesto1": 8,
        "impuesto2": 0,
        "pjedesc": 0,
        "pjedesc2": 0,
        "obs": ""
      }
    ],
    "uuid": "7547e8b1ce9245c29bf0c5e906acf5cf",
    "datoscfdi": "{\"formapago\":\"01\",\"metodopago\":\"PUE\",\"usocfdi\":\"G03\",\"tiporelacion\":\"\",\"cfdirelacionado\":\"\",\"condicionesdepago\":\"\"}",
    "usocfdi": "G03",
    "metodopagosat": "PUE",
    "formapagosat": "01",
    "formapago": "1",
    "condicionesdepago": "",
    "cfdirelacionados": "",
    "tiporelacion": "",
    "CfdiRes": null,
}
```