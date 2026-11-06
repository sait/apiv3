## Notas de Credito

Rutas de Notas

| Accion                                    | Ruta                                              |
| ----------------------------------------- | ------------------------------------------------- |
| Crear                                     | POST  /api/v3/notascred/                          |
| [Listar](#listar)                         | GET   /api/v3/notascred?filters...                |
| [Totales](#totales-de-notas-de-credito)   | GET   /api/v3/notascred?totalizar=true&filters... |


Campos de las Notas

| Campo      | Significado                                                     |
| ---------- | --------------------------------------------------------------- |
| id         | Id incremental disponible solo através de SAIT API              |
| created    | Timestamp del momento de creación                               |
| updated    | Timestamp ultima actualizacion                                  |
| tipodoc    | Puede tener 2 caracteres que lo identifiquen, En este caso "DV" |
| numdoc     | Clave de identificación del documento.                          |
| pagos      | Pagos realizados en caja. Cada línea representa un movimiento.  |
| formapago  | El tipo de pago a usar. 1.-Contado2.-Credito                    |
| fecha      | Fecha en que se proceso el documento                            |
| hora       | Hora en que se proceso el documento                             |
| divisa     | Tipo de divisa usada "P" (pesos) o "D" (dólares).               |
| tc         | Tipo de cambio usado                                            |
| importe    | Total del precio de los artículos (Precio base x Cantidad)      |
| descuento  | Descuento aplicado al importe                                   |
| impuesto1  | primer impuesto aplicado (IVA)                                  |
| impuesto2  | primer impuesto aplicado (IEPS)                                 |
| numalm     | Identificación de la sucursal donde se procesa el documento     |
| status     | Status del documento                                            |
| datoscfdi  | JSON con los datos del cfdi, relacion, metodos de pago, uso.    |
| numfact    | Clave de la factura                                             |
| items      | Slice de los movimientos asociados a la nota                    |

Campos de los Items

| Campo      | Significado                                                     |
| ---------- | --------------------------------------------------------------- |
| tipodoc    | Puede tener 2 caracteres que lo identifiquen, En este caso " N" |
| numdoc     | Clave de identificación del documento.                          |
| numpar     | Número de partida                                               |
| precio     | Precio del item                                                 |
| pjedesc    | Porcentaje de descuento                                         |
| impuesto1  | Porcentaje de impuesto1 (IVA)                                   |
| impuesto2  | Porcentaje de impuesto2 (IEPS)                                  |
| cant       | Cantidad de artículos que se movieron                           |
| unidad     | Unidad en que se manejas los artículos                          |
| excento    |                                                                 |
| obs        | Observaciones de movimiento                                     |

---
### Crear Nota por Clave

POST /api/v3/notas/:numdoc

Request:
```json
{
	"numdoc":    "AF43",
	"fecha":     "2024-06-04",
	"hora":      "9:57:24",
	"numcli":    "1203",
	"numalm":    " 1",
	"divisa":    "P",
	"importe":   224.18,
	"descuento": 0,
	"impuesto1": 35.87,
	"impuesto2": 0,
	"total":     260.05,
	"usoCFDI":           "S01",
	"metodoPagoSat":     "PUE",
	"formaPagoSat":      "01",
    "formaPago":         "1",
	"condicionesdepago": "CONTADO",
	"cfdiRelacionados":  "402CFE01D6764A72A6A128D972152E3C",
	"tipoRelacion":      "01",
	"items": [
		{
			"cant":      1,
			"numart":    "FLASH",
			"unidad":    "CAJA",
			"precio":    180.18103,
			"impuesto1": 16,
			"impuesto2": 0,
			"Pjedesc":   0
		},
		{
			"cant":      1,
			"numart":    "ABRE",
			"unidad":    "PZA",
			"precio":    44,
			"impuesto1": 16,
			"impuesto2": 0,
			"pjedesc":   0
		}
    ]
}

```

Response:
```json
{
    "result":"CREATED"
}
```

---
### Listar

GET /api/v3/notascred?filters...

Regresa los encabezados de las notas de credito (tipodoc="DV"), sin partidas.

| Filtros  | significado                                                    | Tipo de filtro         | Uso                                                                     |
|----------|----------------------------------------------------------------|------------------------|-------------------------------------------------------------------------|
| offset   | A partir del registro a iniciar. Default 0                     | despues de             | desde que registro empezar: offset=10                                   |
| limit    | Cuantos registros obtener. Default 100                         | cantidad               | cuantos registros recibir: limit=50                                     |
| q        | Palabras a buscar (numdoc, numcli, nomcli, numcliev, nomcliev) | busqueda por similitud | traer notas donde numdoc,numcli,nomcli sean similares a q=NC134         |
| numdoc   | clave de nota de credito                                       | busqueda por exactitud | traer notas cuando numdoc="NC10001"                                     |
| uuid     | uuid de nota de credito                                        | busqueda por exactitud | traer notas cuando uuid="E2D8896557F14ADFAF3F03F7853DDAD2"              |
| numalm   | clave de almacen                                               | busqueda por exactitud | traer notas cuando numalm=" 1"                                          |
| numuser  | clave de usuario                                               | busqueda por exactitud | traer notas cuando numuser="  MSL"                                      |
| numcli   | clave de cliente                                               | busqueda por exactitud | traer notas cuando numcli="  B10"; si contiene "-" filtra por numcliev  |
| numvend  | clave de vendedor                                              | busqueda por exactitud | traer notas cuando numvend="  MSL"                                      |
| numzona  | zona del cliente                                               | busqueda por exactitud | traer notas de clientes con numzona="1"                                 |
| clasif   | clasificacion del cliente                                      | busqueda por exactitud | traer notas de clientes con clasif="A"                                  |
| status   | status de la nota de credito                                   | busqueda por exactitud | 0=vigentes, 1=canceladas, ""=todas las notas                            |
| divisa   | divisa de la nota de credito                                   | busqueda por exactitud | P=MXN, D=USD                                                            |
| fecha1   | traer las notas con fecha mayor o igual a fecha1               | busqueda mayor a       | traer notas con fecha de registro mayor a "2025-03-21"                  |
| fecha2   | traer las notas con fecha menor o igual a fecha2               | busqueda menor a       | traer notas con fecha de registro menor a "2025-03-29"                  |
| order    | campo por el cual ordenar, anteponer "-" para descendente      | ordenar notas          | id, created, updated, numdoc, fecha, total. Ej: order=-fecha            |
| format   | generar reporte en lugar de JSON (ignora limit y offset)       | reporte                | pdf, xlsx                                                               |

Response
```json
[
  {
    "id": "2283741",
    "created": "2024-07-10 08:51:23",
    "updated": "2024-07-10 08:51:23",
    "tipodoc": "DV",
    "numdoc": "      NC15",
    "status": "0",
    "tc": "17.4",
    "fecha": "2024-07-10",
    "divisa": "P",
    "moneda": "MXN",
    "uuid": "7547e8b1ce9245c29bf0c5e906acf5cf",
    "importe": "4.17",
    "impuesto1": "0.33",
    "impuesto2": "0",
    "descuento": "0",
    "total": "4.5",
    "nomcli": "Publico general",
    "numcli": "    0"
  }
]
```

---
### Totales de notas de credito

Totales agrupados por Divisa, en el caso de la moneda "ZZZ" se refiere a la suma de los totales de todas las divisas convertidas a pesos mexicanos

GET /api/v3/notascred?totalizar=true&filters...

Acepta los mismos filtros que [Listar](#listar), excepto `q`, `limit`, `offset`, `order` y `format`.
Si se envia `onlyitems=true` solo regresa la cantidad de notas: `{"items": "1203"}`.

Response
```json
{
  "total": {
    "descuento": 314.15,
    "importe": 39152.85,
    "impuesto1": 3003.02,
    "impuesto2": 7.33,
    "items": 120,
    "moneda": "ZZZ",
    "total": 41849.05,
    "importe_mxn": 0,
    "imp1_mxn": 0,
    "imp2_mxn": 0,
    "desc_mxn": 0,
    "total_mxn": 0
  },
  "totalesmoneda": [
    {
      "descuento": 314.15,
      "importe": 39152.85,
      "impuesto1": 3003.02,
      "impuesto2": 7.33,
      "items": 120,
      "moneda": "MXN",
      "total": 41849.05,
      "importe_mxn": 39152.85,
      "imp1_mxn": 3003.02,
      "imp2_mxn": 7.33,
      "desc_mxn": 314.15,
      "total_mxn": 41849.05
    }
  ]
}
```
