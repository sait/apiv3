## Facturas

Rutas de facturas

| Accion                            | Ruta                           |
|-----------------------------------|--------------------------------|
| [Leer por clave](#leer-por-clave) | GET   /api/v3/facturas/:numdoc |
| [Listar](#listar)                 | GET   /api/v3/facturas         |

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

---
### Listar

GET /api/v3/facturas?filters...

| Filtros | significado                                          | Tipo de filtro         | Uso                                                                   |
|---------|------------------------------------------------------|------------------------|-----------------------------------------------------------------------|
| offset  | A partir de que registro iniciar búsqueda. Default 0 | despues de             | desde que registro empezar: offset=10                                 |
| limit   | Cuantos registros obtener. Default 100               | cantidad               | cuantos registros recibir: limit=50                                   |
| q       | Palabras a buscar (numdoc, nomcli,nomcliev)          | busqueda por similitud | traer facturas donde numdoc,numcli,nomcliev sean similares a q=AF134  |
| numalm  | clave de almacen                                     | busqueda por exactitud | traer facturas cuando numalm=" 1"                                     |
| numuser | clave de usuario                                     | busqueda por exactitud | traer facturas cuando numuser="  MSL"                                 |
| numcli  | clave de cliente                                     | busqueda por exactitud | traer facturas cuando numcli="  B10" o numcliev="       B10"          |
| numvend | clave de vendedor                                    | busqueda por exactitud | traer facturas cuando numvend="  MSL"                                 |
| fecha1  | traer todos los documentos mayores a fecha1          | busqueda mayor a       | traer facturas con fecha de registro mayor a "2025-03-21"             |
| fecha2  | traer todos los documentos menores a fecha2          | busqueda menor a       | traer facturas con fecha de registro menor a "2025-03-29"             |
| precio1 | traer todos los documentos con total mayor a precio1 | busqueda mayor a       | traer facturas con total de venta mayor a 50 pesos                    |
| precio2 | traer todos los documentos con total menor a precio2 | busqueda menor a       | traer facturas con total de venta menor a 250 pesos                   |
| order   |                                                      | ordenar facturas       | ordenar facturas segun updated,id,numdoc,fecha,numcli,numvend,numuser |

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
    "numcli": "    0",
    "importe": 4.17,
    "descuento": 0,
    "impuesto1": 0.33,
    "impuesto2": 0,
    "total": 4.5,
  }
]

```