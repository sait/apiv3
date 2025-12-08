## Notas de Credito

Rutas de Notas

| Accion         | Ruta                                  |
| -------------- | ------------------------------------- |
| Crear          | POST  /api/v3/notascred/              |


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
