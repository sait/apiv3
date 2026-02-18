## Facturas

Rutas de facturas

| Accion                            | Ruta                                             |
|-----------------------------------|--------------------------------------------------|
| [Leer por clave](#leer-por-clave) | GET   /api/v3/facturas/:clave                    |
| [Listar](#listar)                 | GET   /api/v3/facturas?filters...                |
| [Totales](#totales-de-facturas)   | GET   /api/v3/facturas?totalizar=true&filters... |

---
### Leer por Clave

- GET /api/v3/facturas/:numdoc
- GET /api/v3/facturas/:uuid

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
    "moneda":"MXN",
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

---
### Listar

GET /api/v3/facturas?filters...

| Filtros  | significado                                 | Tipo de filtro         | Uso                                                                   |
|----------|---------------------------------------------|------------------------|-----------------------------------------------------------------------|
| offset   | A partir del registro a iniciar. Default 0  | despues de             | desde que registro empezar: offset=10                                 |
| limit    | Cuantos registros obtener. Default 100      | cantidad               | cuantos registros recibir: limit=50                                   |
| q        | Palabras a buscar (numdoc, nomcli,nomcliev) | busqueda por similitud | traer facturas donde numdoc,numcli,nomcliev sean similares a q=AF134  |
| numdoc   | clave de factura                            | busqueda por exactitud | traer facturas cuando numdoc="AF10001"                                |
| uuid     | uuid de factura                             | busqueda por exactitud | traer facturas cuando uuid="E2D8896557F14ADFAF3F03F7853DDAD2"         |
| numalm   | clave de almacen                            | busqueda por exactitud | traer facturas cuando numalm=" 1"                                     |
| numuser  | clave de usuario                            | busqueda por exactitud | traer facturas cuando numuser="  MSL"                                 |
| numcli   | clave de cliente                            | busqueda por exactitud | traer facturas cuando numcli="  B10" o numcliev="       B10"          |
| numvend  | clave de vendedor                           | busqueda por exactitud | traer facturas cuando numvend="  MSL"                                 |
| tipofact | tipo de factura                             | busqueda por exactitud | 1=Normales, 2=Globales, 3=De notas, 3=De Autofacturador, omitir=todas |
| status   | status de la factura                        | busqueda por exactitud | 1=canceladas, ""=todas las facturas                                   |
| fecha1   | traer las facturas mayores a fecha1         | busqueda mayor a       | traer facturas con fecha de registro mayor a "2025-03-21"             |
| fecha2   | traer las facturas menores a fecha2         | busqueda menor a       | traer facturas con fecha de registro menor a "2025-03-29"             |
| order    |                                             | ordenar facturas       | ordenar facturas segun updated,numdoc,fecha, total                    |

Response
```json
[
  {
    "id": 2283739,
    "created": "2024-07-10 08:51:23",
    "updated": "2026-01-06 14:30:33",
    "tipodoc": " F",
    "numdoc": "       AF2",
    "tc": 17.4,
    "fecha": "2024-07-10",
    "divisa": "P",  
    "moneda":"MXN",
    "numcli": "    0",
    "nomcli": "Publico general",
    "importe": 4.17,
    "descuento": 0,
    "impuesto1": 0.33,
    "impuesto2": 0,
    "total": 4.5,
  }
]
```

---
### Totales de facturas

Totales agrupados por Divisa, en el caso de la divisa "X" se refiere a la suma de los totales de todas las divisas convertidas a pesos mexicanos

GET /api/v3/facturas?totalizar=true&filters...

| Filtros  | significado                         | Tipo de filtro         | Uso                                                                   |
|----------|-------------------------------------|------------------------|-----------------------------------------------------------------------|
| numdoc   | clave de factura                    | busqueda por exactitud | traer facturas cuando numdoc="AF10001"                                |
| uuid     | uuid de factura                     | busqueda por exactitud | traer facturas cuando uuid="E2D8896557F14ADFAF3F03F7853DDAD2"         |
| numalm   | clave de almacen                    | busqueda por exactitud | traer facturas cuando numalm=" 1"                                     |
| numuser  | clave de usuario                    | busqueda por exactitud | traer facturas cuando numuser="  MSL"                                 |
| numcli   | clave de cliente                    | busqueda por exactitud | traer facturas cuando numcli="  B10" o numcliev="       B10"          |
| numvend  | clave de vendedor                   | busqueda por exactitud | traer facturas cuando numvend="  MSL"                                 |
| tipofact | tipo de factura                     | busqueda por exactitud | 1=Normales, 2=Globales, 3=De notas, 3=De Autofacturador, omitir=todas |
| status   | status de la factura                | busqueda por exactitud | 1=canceladas, ""=todas las facturas                                   |
| fecha1   | traer las facturas mayores a fecha1 | busqueda mayor a       | traer facturas con fecha de registro mayor a "2025-03-21"             |
| fecha2   | traer las facturas menores a fecha2 | busqueda menor a       | traer facturas con fecha de registro menor a "2025-03-29"             |
| order    |                                     | ordenar facturas       | ordenar facturas segun updated,numdoc,fecha, total                    |

Response
```json
[
  {
    "descuento": "84917.750000",
    "moneda": "MXN",
    "importe": "145039011.720000",
    "impuesto1": "11184892.610000",
    "impuesto2": "2379.640000",
    "items": "29460",
    "total": "156141366.220000"
  },
  {
    "descuento": "84917.750000",
    "moneda": "ZZZ",
    "importe": "145039011.720000",
    "impuesto1": "11184892.610000",
    "impuesto2": "2379.640000",
    "items": "29460",
    "total": "156141366.220000"
  }
]
```