## Compras

| Accion                                      | Ruta                                                      |
|---------------------------------------------|-----------------------------------------------------------|
| [Consultas generales](#consultas-generales) | POST   /api/v3/compras/:pk/consultas_generales?filtros... |

Campos consultas generales

| Campo       | Tipo   | Significado                                                                                                                                 |
|-------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------|
| folio       | string | numero de documento (numdoc)                                                                                                                |
| proveedor   | string | numero y nombre de proveedor concatenados: "(numprov) nombre"                                                                               |
| fecha       | string | fecha del documento                                                                                                                         |
| factura     | string | numero de referencia / factura relacionada al documento                                                                                     |
| divisa      | string | tipo de moneda del documento ("P": pesos, cualquier otro valor: moneda extranjera)                                                          |
| total_mn    | float  | importe total en moneda nacional (solo si divisa = "P", de lo contrario 0)                                                                  |
| total_me    | float  | importe total en moneda extranjera (solo si divisa != "P", de lo contrario 0)                                                               |
| tc          | float  | tipo de cambio aplicado al documento                                                                                                        |
| status      | string | estado actual del documento                                                                                                                 |
| uuid        | string | UUID fiscal (CFDI) relacionado al documento                                                                                                 |
| importe_mn  | float  | importe neto convertido a moneda nacional: (importe - descuento + impuesto1 + impuesto2 - descuentog) x tc, si divisa = "P" no se aplica tc |
| importe_dls | float  | importe neto en moneda extranjera (mismo calculo, sin conversion de tc)                                                                     |

> Nota: si el usuario no cuenta con permisos para ver costos, `importe_mn` e `importe_dls` se devuelven en 0.0.

---
### Consultas generales

POST /api/v3/compras/:pk/consultas_generales?filtros...

`:pk` (obligatorio) corresponde al **tipo de documento** a consultar:

| Valor | Significado            |
|-------|------------------------|
| " C"  | Compras                |
| "DC"  | Devoluciones de compra |
| " O"  | Ordenes                |

filtros

| Filtro    | Descripcion                                        |
|-----------|----------------------------------------------------|
| numalm    | (obligatorio) Numero de identificacion del almacen |
| fecha1    | A partir de tal fecha para tomar registros         |
| fecha2    | Hasta tal fecha para tomar registros               |
| folio1    | Folio inicial del rango a consultar                |
| folio2    | Folio final del rango a consultar                  |
| divisa    | Filtra por tipo de divisa exacta                   |
| proveedor | Numero de identificacion del proveedor             |
| limit     | cantidad de registros a tomar                      |
| offset    | tomar registros a partir del registro X            |

Response
```json
[
  {
    "folio": "    A41512",
    "proveedor": "(  123) PROVEEDOR EJEMPLO SA DE CV",
    "fecha": "2020-01-04",
    "factura": "F-0098",
    "divisa": "P",
    "total_mn": "9800.00",
    "total_me": "0",
    "tc": "1.0000",
    "status": "A",
    "uuid": "1a2b3c4d-...-...",
    "importe_mn": "9536.00",
    "importe_dls": "0.00"
  },
  {}...
]
```

Totalizar resultado:

POST /api/v3/compras/:pk/consultas_generales?filtros...&totalizar=true

response:
```json
{
  "items": "18778"
}
```