## Compras

Rutas de compras (documentos de la tabla `docum`)

| Accion                         | Ruta                                            |
|--------------------------------|-------------------------------------------------|
| [Listar](#listar)              | GET   /api/v3/compras?filters...                |
| [Totales](#totales-de-compras) | GET   /api/v3/compras?totalizar=true&filters... |

---
### Listar

GET /api/v3/compras?filters...

Regresa los encabezados de los documentos de compra (sin partidas). Si no se envia `limit` se regresan todos los registros.

| Filtros   | significado                                         | Tipo de filtro         | Uso                                                                  |
|-----------|-----------------------------------------------------|------------------------|----------------------------------------------------------------------|
| offset    | A partir del registro a iniciar                     | despues de             | desde que registro empezar: offset=10                                |
| limit     | Cuantos registros obtener                           | cantidad               | cuantos registros recibir: limit=50                                  |
| tipodoc   | tipo de documento. Default " C"                     | busqueda por exactitud | " C"=Compras, "DC"=Devoluciones de compra, " O"=Ordenes              |
| q         | Palabras a buscar (numdoc, refer, numprov, nomprov) | busqueda por similitud | traer compras donde numdoc,refer,numprov,nomprov sean similares a q=F-0098 |
| numdoc    | clave de compra                                     | busqueda por exactitud | traer compras cuando numdoc="A41512"                                 |
| numalm    | clave de almacen                                    | busqueda por exactitud | traer compras cuando numalm=" 1"                                     |
| numuser   | clave de usuario                                    | busqueda por exactitud | traer compras cuando numuser="  MSL"                                 |
| numprov   | clave de proveedor                                  | busqueda por exactitud | traer compras cuando numprov="  123"                                 |
| status    | status de la compra                                 | busqueda por exactitud | 1=canceladas, ""=todas las compras                                   |
| divisa    | divisa de la compra                                 | busqueda por exactitud | P=MXN, D=USD                                                         |
| fecha1    | traer las compras con fecha mayor o igual a fecha1  | busqueda mayor a       | traer compras con fecha de registro mayor a "2025-03-21"             |
| fecha2    | traer las compras con fecha menor o igual a fecha2  | busqueda menor a       | traer compras con fecha de registro menor a "2025-03-29"             |
| totalizar | regresar totales en lugar del listado               | totales                | ver [Totales de compras](#totales-de-compras)                        |

Response
```json
[
  {
    "id": 1830021,
    "created": "2020-01-04 10:21:13",
    "updated": "2020-01-04 10:21:13",
    "tipodoc": " C",
    "numdoc": "    A41512",
    "status": "",
    "tc": "1",
    "fecha": "2020-01-04",
    "divisa": "P",
    "moneda": "MXN",
    "refer": "F-0098",
    "uuid": "1a2b3c4d-...",
    "importe": "8220.69",
    "impuesto1": "1315.31",
    "impuesto2": "0",
    "descuento": "0",
    "total": "9536",
    "numprov": "  123",
    "nomprov": "PROVEEDOR EJEMPLO SA DE CV"
  }
]
```

| Campo   | significado                                                         |
|---------|---------------------------------------------------------------------|
| refer   | numero de referencia / factura del proveedor                        |
| total   | importe + impuesto1 + impuesto2 - descuento - descuentog            |

---
### Totales de compras

Totales agrupados por Divisa, en el caso de la moneda "ZZZ" se refiere a la suma de los totales de todas las divisas convertidas a pesos mexicanos

GET /api/v3/compras?totalizar=true&filters...

Acepta los mismos filtros que [Listar](#listar), excepto `limit` y `offset`.
Si se envia `onlyitems=true` solo regresa la cantidad de documentos: `{"items": "1203"}`.

Response
```json
{
  "total": {
    "descuento": 89865.61,
    "importe": 2002011.28,
    "impuesto1": 320321.8,
    "impuesto2": 0,
    "items": 1203,
    "moneda": "ZZZ",
    "total": 2232467.47
  },
  "totalesmoneda": [
    {
      "descuento": 314.15,
      "importe": 1915.33,
      "impuesto1": 306.45,
      "impuesto2": 0,
      "items": 3,
      "moneda": "DLS",
      "total": 1907.63
    },
    {
      "descuento": 84603.6,
      "importe": 1968700.12,
      "impuesto1": 314992.02,
      "impuesto2": 0,
      "items": 1200,
      "moneda": "MXN",
      "total": 2199088.54
    }
  ]
}
```
