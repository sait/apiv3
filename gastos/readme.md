## Gastos

| Accion                                      | Ruta                                                     |
|---------------------------------------------|-----------------------------------------------------------|
| [Consultas generales](#consultas-generales) | POST   /api/v3/gastos/:pk/consultas_generales?filtros... |

Campos consultas generales

| Campo     | Tipo   | Significado                                                              |
|-----------|--------|---------------------------------------------------------------------------|
| fecha     | string | fecha del documento                                                       |
| numprov   | string | numero de identificacion del proveedor                                   |
| proveedor | string | nombre del proveedor (si no tiene nombre registrado: "Sin nombre")        |
| numdoc    | string | numero de documento (folio)                                              |
| total     | float  | importe total del gasto, redondeado a 4 decimales                        |
| divisa    | string | tipo de moneda del documento ("P": pesos, cualquier otro valor: moneda extranjera) |
| uuid      | string | UUID fiscal (CFDI) relacionado al documento, vacio si no tiene CFDI      |
| rfc       | string | RFC asociado al documento                                                |

---
### Consultas generales

POST /api/v3/gastos/:pk/consultas_generales?filtros...

`:pk` (obligatorio) corresponde al **tipo de gasto** a consultar (idregla).

filtros

| Filtro  | Descripcion                                                              |
|---------|---------------------------------------------------------------------------|
| numalm  | Numero de identificacion del almacen                                     |
| numprov | Numero de identificacion del proveedor                                   |
| fecha1  | A partir de tal fecha para tomar registros                               |
| fecha2  | Hasta tal fecha para tomar registros                                     |
| folio1  | Folio inicial del rango a consultar                                      |
| folio2  | Folio final del rango a consultar                                        |
| cfdis   | Filtra por presencia de CFDI: "S" = sin CFDI, "C" = con CFDI             |
| limit   | cantidad de registros a tomar                                            |
| offset  | tomar registros a partir del registro X                                  |

Response
```json
[
  {
    "fecha": "2020-01-04",
    "numprov": "  123",
    "proveedor": "PROVEEDOR EJEMPLO SA DE CV",
    "numdoc": "    A41512",
    "total": "9800.0000",
    "divisa": "P",
    "uuid": "1a2b3c4d-...-...",
    "rfc": "XAXX010101000"
  },
  {}...
]
```

Totalizar resultado:

POST /api/v3/gastos/:pk/consultas_generales?filtros...&totalizar=true

response:
```json
{
    "items": "8452",
    "total": "1204533.10"
}
```