## Pedidos

Rutas de pedidos

| Accion                      | Ruta                          |
|-----------------------------|-------------------------------|
| [Crear](#crear)             | POST  /api/v3/pedidos         |
| [Listado](#listado-pedidos) | GET   /api/v3/pedidos         |
| [Leer](#leer-pedido)        | GET   /api/v3/pedidos/:numdoc |

**Campos pedido**

| campos     | tipo de dato  | descripcion                                                 |
|------------|---------------|-------------------------------------------------------------|
| id         | int           | id incremental disponible solo através de SAIT API          |
| created    | datetime      | timestamp del momento de creación                           |
| updated    | datetime      | timestamp ultima actualizacion                              |
| tipodoc    | string        | tipo de documento (" P": pedido)                            |
| numdoc     | string        | numero de documento                                         |
| numcli     | string        | numero de cliente                                           |
| numcliev   | string        | numero de cliente eventual                                  |
| numvend    | string        | numero de vendedor                                          |
| fecha      | string        | Fecha de creacion del documento                             |
| fechacapt  | string        | fecha e captura del documento                               |
| hora       | string        | hora de creacion del documento                              |
| divisa     | string        | divisa del documento (P: Pesos o D: Dolares)                |
| tc         | float64       | tipo de cambio (solo entre pesos y dolares)                 |
| importe    | float64       | total del precio de las partidas                            |
| descuento  | float64       | descuento al importe                                        |
| impuesto1  | float64       | total de IVA a pagar                                        |
| Impuesto2  | float64       | total de IEPS a pagar                                       |
| formapago  | string        | el tipo de pago a usar. 1.-Contado2.-Credito                |
| numalm     | string        | identificación de la sucursal donde se procesa el documento |
| obs        | string        | Observaciones                                               |
| items      | []*items      | lista de apuntadores de los items (partidas)                |
| otrosdatos | []*OtrosDatos | lista de apuntadores de los items (partidas)                |

**Campos Item**

| campos    | tipo de dato | descripcion                        |
|-----------|--------------|------------------------------------|
| tipodoc   | string       | tipo de documento                  |
| numdoc    | string       | numero de documento                |
| numpar    | string       | numero de partida                  |
| numart    | string       | numero de articulo                 |
| precio    | float64      | precio del articulo por 1 cantidad |
| pjedesc   | float64      | porcentaje de descuento            |
| impuesto1 | float64      | porcentaje de IVA                  |
| impuesto2 | float64      | porcentaje de IEPS                 |
| pend      | float64      | cantidad pendiente de entrega      |
| cant      | float64      | cantidad de partida                |
| unidad    | string       | unidad de articulo                 |
| obs       | string       | Observaciones                      |

---

### Crear

POST /api/v3/pedidos

**Dryrun para validar los datos sin crear el pedido:**

POST /api/v3/pedidos?dryrun=true

request
```json
{
	"numvend":"MSL",
	"numcli":"0",
	"numcliev":"",
	"numalm":"1",
	"formapago":"1",
	"divisa":"P",
  "fentrega":"20250620",
  "hentrega":"15:30",
	"obs":"",
	"items":[
		{
			"numart":"528790",
			"pjedesc":0,
			"precio":34.25925925925926,
			"cant":6,
			"unidad":"PZA"
		},
		{
			"numart":"SM477-11",
			"pjedesc":10,
			"precio":458.3333,
			"cant":1,
			"unidad":"PZA"
		},
		{
			"numart":"528790",
			"pjedesc":0,
			"precio":34.25925925925926,
			"cant":4,
			"unidad":"PZA"
		}  
	]
}
```

response
```json
{
  "result": {
    "id": 2006145,
    "created": "2025-06-17 00:54:02",
    "updated": "2025-06-17 00:54:02",
    "tipodoc": " P",
    "numdoc": "       OT1",
    "numcli": "    0",
    "nomcli": "C O N T A D O",
    "numvend": "  MSL",
    "pagos": "",
    "fecha": "2025-06-16",
    "fechacapt": "2025-06-16",
    "hora": "17:54:02",
    "fentrega": "2025-06-20",
    "hentrega": "15:30",
    "divisa": "P",
    "tc": 22,
    "importe": 800.93,
    "descuento": 45.83,
    "impuesto1": 60.41,
    "impuesto2": 0,
    "formapago": "1",
    "numalm": " 1",
    "obs": "",
    "total": 815.51,
    "items": [
      {
        "tipodoc": " P",
        "numdoc": "       OT1",
        "numpar": "  1",
        "numart": "              528790",
        "desc": "CUADERNO NORMA ITALIANO PELUCHES COSIDO RAYA C/100H (D)",
        "codigo": "       7702111287907",
        "precio": 34.25926,
        "pjedesc": 0,
        "impuesto1": 8,
        "impuesto2": 0,
        "pend": 6,
        "cant": 6,
        "unidad": "PZA",
        "obs": ""
      },
      {
        "tipodoc": " P",
        "numdoc": "       OT1",
        "numpar": "  2",
        "numart": "            SM477-11",
        "desc": "BLOCK STRATHMORE P/LAPIZ DE COLOR 11X14\" C/30",
        "codigo": "        012017621116",
        "precio": 458.3333,
        "pjedesc": 10,
        "impuesto1": 8,
        "impuesto2": 0,
        "pend": 1,
        "cant": 1,
        "unidad": "PZA",
        "obs": ""
      },
      {
        "tipodoc": " P",
        "numdoc": "       OT1",
        "numpar": "  3",
        "numart": "              528790",
        "desc": "CUADERNO NORMA ITALIANO PELUCHES COSIDO RAYA C/100H (D)",
        "codigo": "       7702111287907",
        "precio": 34.25926,
        "pjedesc": 0,
        "impuesto1": 8,
        "impuesto2": 0,
        "pend": 4,
        "cant": 4,
        "unidad": "PZA",
        "obs": ""
      }
    ]
  },
  "error": ""
}
```

---
### Leer pedido

GET /api/v3/pedidos/:numdoc

response:
```json
{
  "id": 2006141,
  "created": "2025-06-10 22:12:01",
  "updated": "2025-06-10 22:12:01",
  "tipodoc": " P",
  "numdoc": "        A4",
  "numcli": "    0",
  "nomcli": "C O N T A D O",
  "numvend": "  919",
  "pagos": "",
  "fecha": "2025-06-03",
  "fechacapt": "2025-06-03",
  "hora": "11:09:17",
  "fentrega": "2025-06-03",
  "hentrega": "11:07",
  "divisa": "P",
  "tc": 25,
  "importe": 2314.8,
  "descuento": 0,
  "impuesto1": 370.37,
  "impuesto2": 0,
  "formapago": "1",
  "numalm": " 1",
  "obs": "",
  "total": 2685.17,
  "items": [
    {
      "tipodoc": " P",
      "numdoc": "        A4",
      "numpar": "  1",
      "numart": "                ABRE",
      "desc": "ABRECUBETAS",
      "codigo": "",
      "precio": 231.48,
      "pjedesc": 0,
      "impuesto1": 16,
      "impuesto2": 0,
      "pend": 10,
      "cant": 10,
      "unidad": "PIEZA",
      "obs": ""
    }
  ],
  "otrosdatosstring": ""
}
```

---
### Listar pedidos

GET /api/v3/pedidos

| Variable  | Significado                                          |
|-----------|------------------------------------------------------|
| offset    | A partir de que registro iniciar búsqueda. Default 0 |
| limit     | Cuantos registros obtener. Default 100               |
| order     | Orden deseado. updated,id, tipodoc,numdoc            |
| q         | Palabras a buscar (numdoc, nomcli,nomcliev)          |
| numcli    | clave de cliente o cliente eventual                  |
| numclisuc | Clave Sucursal de cliente                            |
| numalm    | Numero de almacen                                    |
| numuser   | Numero de usuario                                    |
| numvend   | clave de vendedor                                    |
| fecha1    | traer todos los documentos mayores a fecha1          |
| fecha2    | traer todos los documentos menores a fecha2          |
| precio1   | traer todos los documentos con total mayor a precio1 |
| precio2   | traer todos los documentos con total menor a precio2 |

response:
```json
{
  [
    {
      "id": 2006141,
      "tipodoc": " P",
      "numdoc": "        A4",
      "numcli": "    0",
      "nomcli": "C O N T A D O",
      "fecha": "2025-06-03",
      "divisa": "P",
      "tc": 25,
      "importe": 2314.8,
      "descuento": 0,
      "impuesto1": 370.37,
      "impuesto2": 0,
      "total": 2685.17
    },
    {
      "id": 2006143,
      "tipodoc": " P",
      "numdoc": "        A5",
      "numcli": "    0",
      "nomcli": "C O N T A D O",
      "fecha": "2025-06-03",
      "divisa": "P",
      "tc": 25,
      "importe": 2314.8,
      "descuento": 0,
      "impuesto1": 370.37,
      "impuesto2": 0,
      "total": 2685.17
    },
    {
      "id": 2005995,
      "tipodoc": " P",
      "numdoc": "    WC2056",
      "numcli": "      AF-7",
      "nomcli": "prueba clievent en pedidos",
      "fecha": "2024-11-12",
      "divisa": "P",
      "tc": 25,
      "importe": 145.27,
      "descuento": 0,
      "impuesto1": 14.73,
      "impuesto2": 0,
      "total": 160,
    },
  ]
}
```
