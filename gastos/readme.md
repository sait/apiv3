## Gastos

| Accion                                      | Ruta                                              |
|---------------------------------------------|---------------------------------------------------|
| [Consultas generales](#consultas-generales) | GET    /api/v3/gastos?filtros...                  |
| [Reporte PDF o XLSX](#reporte-en-pdf-o-xlsx) | GET    /api/v3/gastos?filtros...&format=pdf       |
| [Totales](#totalizar-resultado)             | GET    /api/v3/gastos?filtros...&totalizar=true   |

Campos consultas generales

| Campo     | Tipo   | Significado                                                              |
|-----------|--------|---------------------------------------------------------------------------|
| fecha     | string | fecha del documento                                                       |
| numprov   | string | numero de identificacion del proveedor                                   |
| proveedor | string | nombre del proveedor (si no tiene nombre registrado: "Sin nombre"). Si numprov es 0, se busca el proveedor por el rfc del gasto; si no se encuentra se muestra el rfc |
| numdoc    | string | numero de documento (folio)                                              |
| total     | float  | importe total del gasto, redondeado a 4 decimales                        |
| divisa    | string | tipo de moneda del documento ("P": pesos, cualquier otro valor: moneda extranjera) |
| uuid      | string | UUID fiscal (CFDI) relacionado al documento, vacio si no tiene CFDI      |
| rfc       | string | RFC asociado al documento                                                |

---
### Consultas generales

GET /api/v3/gastos?filtros...

filtros

| Filtro  | Descripcion                                                              |
|---------|---------------------------------------------------------------------------|
| idregla | Tipo de gasto a consultar. Si no se envia o es 0 se muestran todos       |
| numalm  | Numero de identificacion del almacen                                     |
| numprov | Numero de identificacion del proveedor                                   |
| fecha1  | A partir de tal fecha para tomar registros                               |
| fecha2  | Hasta tal fecha para tomar registros                                     |
| folio   | Folio (numdoc) exacto a consultar                                        |
| order   | Orden: `fecha`, `numdoc`, `numprov` o `total`, anteponer `-` para descendente |
| limit   | cantidad de registros a tomar                                            |
| offset  | tomar registros a partir del registro X                                  |
| format  | `pdf` o `xlsx` para regresar el listado como archivo (ver abajo)         |

| Ejemplo                                | Ruta                                                       |
|----------------------------------------|------------------------------------------------------------|
| Gastos de todos los tipos              | /api/v3/gastos?limit=50                                     |
| Gastos del tipo 3 en enero 2026        | /api/v3/gastos?idregla=3&fecha1=2026-01-01&fecha2=2026-01-31 |
| Gastos de un proveedor en un almacen   | /api/v3/gastos?numprov=123&numalm=1                         |
| Buscar un folio                        | /api/v3/gastos?folio=A41512                                 |
| Gastos mas recientes primero           | /api/v3/gastos?order=-fecha&limit=50                        |

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

### Reporte en PDF o XLSX

GET /api/v3/gastos?filtros...&format=pdf

Usa los mismos filtros, incluidos `limit` y `offset`. Las columnas son fecha, folio, proveedor, RFC, moneda, total y UUID, y en el encabezado aparecen los filtros aplicados. La respuesta trae el archivo en base64 y el nombre sugerido:

```json
{
    "archivo": "JVBERi0xLjMKMyAwIG9i...",
    "nombre": "xaxx010101000-consulta-de-gastos-20261111-172304.pdf"
}
```

### Totalizar resultado

GET /api/v3/gastos?filtros...&totalizar=true

Acepta los mismos filtros, excepto `limit`, `offset` y `format`.

response:
```json
{
    "items": "8452",
    "total_gastos": "1204533.10"
}
```

Si se envia `onlyitems=true` solo regresa la cantidad de gastos, sin sumar el total:

GET /api/v3/gastos?filtros...&totalizar=true&onlyitems=true

response:
```json
{
    "items": "8452"
}
```