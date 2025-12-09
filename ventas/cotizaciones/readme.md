## Cotizaciones

Rutas de Cotizaciones

| Accion                            | Ruta                                         |
|-----------------------------------|----------------------------------------------|
| [Crear](#crear)                   | POST  /api/v3/cotizaciones                   |
| [Leer por clave](#leer-por-clave) | GET   /api/v3/cotizaciones/:numdoc?extended= |
| [Actualizar](#actualizar)         | PUT   /api/v3/cotizaciones/:numdoc           |
| [Listar](#listar)                 | GET   /api/v3/cotizaciones?filters...        |

**Campos cotizacion**

| campos    | tipo de dato | descripcion                                                 |
|-----------|--------------|-------------------------------------------------------------|
| id        | int          | id incremental disponible solo através de SAIT API          |
| created   | datetime     | timestamp del momento de creación                           |
| updated   | datetime     | timestamp ultima actualizacion                              |
| tipodoc   | string       | tipo de documento (" Q": Cotizacion)                        |
| numdoc    | string       | numero de documento                                         |
| numcli    | string       | numero de cliente                                           |
| numcliev  | string       | numero de cliente eventual                                  |
| numvend   | string       | numero de vendedor                                          |
| fecha     | string       | Fecha de creacion del documento                             |
| fechacapt | string       | fecha e captura del documento                               |
| hora      | string       | hora de creacion del documento                              |
| divisa    | string       | divisa del documento (P: Pesos o D: Dolares)                |
| tc        | float64      | tipo de cambio (solo entre pesos y dolares)                 |
| importe   | float64      | total del precio de las partidas                            |
| descuento | float64      | descuento al importe                                        |
| impuesto1 | float64      | total de IVA a pagar                                        |
| Impuesto2 | float64      | total de IEPS a pagar                                       |
| formapago | string       | el tipo de pago a usar. 1.-Contado2.-Credito                |
| numalm    | string       | identificación de la sucursal donde se procesa el documento |
| obs       | string       | Observaciones                                               |
| items     | []*items     | lista de apuntadores de los items (partidas)                |

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

POST /api/v3/cotizaciones

```
**Dryrun para validar los datos sin crear la cotizacion:**

POST /api/v3/cotizaciones?dryrun=true
```

request
```json
{
	"numvend":"MSL",
	"numcli":"0",
	"numcliev":"",
	"numalm":"38",
	"formapago":"1",
	"divisa":"P",
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

result
```json
{
    "id":2006081,
    "created":"2025-02-13 19:06:59",
    "updated":"2025-02-13 19:06:59",
    "tipodoc":" Q",
    "numdoc":"      OT79",
    "numcli":"    0",
    "numcliev":"          ",
    "numvend":"  MSL",
    "fecha":"2025-02-13",
    "fechacapt":"2025-02-13",
    "hora":"12:06:59",
    "divisa":"P",
    "tc":21.1,
    "importe":800.93,
    "descuento":45.83,
    "impuesto1":60.41,
    "impuesto2":0,
    "formapago":"1",
    "numalm":"38",
    "obs":"",
    "items":[
        {
            "tipodoc":" Q",
            "numdoc":"      OT79",
            "numpar":"  1",
            "numart":"              528790",
            "precio":34.25926,
            "pjedesc":0,
            "impuesto1":8,
            "impuesto2":0,
            "pend":6,
            "cant":6,
            "unidad":"PZA",
            "obs":""
        },
        {
            "tipodoc":" Q",
            "numdoc":"      OT79",
            "numpar":"  2",
            "numart":"            SM477-11",
            "precio":458.3333,
            "pjedesc":10,
            "impuesto1":8,
            "impuesto2":0,
            "pend":1,
            "cant":1,
            "unidad":"PZA",
            "obs":""
        },
        {
            "tipodoc":" Q",
            "numdoc":"      OT79",
            "numpar":"  3",
            "numart":"              528790",
            "precio":34.25926,
            "pjedesc":0,
            "impuesto1":8,
            "impuesto2":0,
            "pend":4,
            "cant":4,
            "unidad":"PZA",
            "obs":""
        }
    ]
}
```

---
### Leer por Clave

GET /api/v3/cotizaciones/:numdoc?extended=

**nota:** al recibir el parametro "extended", la respuesta se complementara con los datos de la empresa, los articulos y el cliente

response:
```json
{
    "id":2006081,
    "created":"2025-02-13 19:06:59",
    "updated":"2025-02-13 19:06:59",
    "tipodoc":" Q",
    "numdoc":"      OT79",
    "numcli":"    0",
    "numcliev":"          ",
    "numvend":"  MSL",
    "fecha":"2025-02-13",
    "fechacapt":"2025-02-13",
    "hora":"12:06:59",
    "divisa":"P",
    "tc":21.1,
    "importe":800.93,
    "descuento":45.83,
    "impuesto1":60.41,
    "impuesto2":0,
    "formapago":"1",
    "numalm":"38",
    "obs":"",
    "items":[
        {
            "tipodoc":" Q",
            "numdoc":"      OT79",
            "numpar":"  1",
            "numart":"              528790",
            "precio":34.25926,
            "pjedesc":0,
            "impuesto1":8,
            "impuesto2":0,
            "pend":6,
            "cant":6,
            "unidad":"PZA",
            "obs":""
        }
    ]
}
```

---

### Actualizar

PUT /api/v3/cotizaciones/OT80

```
**Dryrun para validar los datos sin actualizar la cotizacion:**

PUT /api/v3/cotizaciones/:numdoc?dryrun=true
```

request
```json
{
	"numvend":"MSL",
	"numcli":"0",
	"numcliev":"",
	"numalm":"38",
	"formapago":"1",
	"divisa":"P",
	"obs":"",
	"items":[
		{
			"numart":"528790",
			"pjedesc":0,
			"precio":34.25925925925926,
			"cant":6,
			"unidad":"PZA"
		}
	]
}
```

result
```json
{
    "id":2006081,
    "created":"2025-02-13 19:06:59",
    "updated":"2025-02-13 19:06:59",
    "tipodoc":" Q",
    "numdoc":"      OT79",
    "numcli":"    0",
    "numcliev":"          ",
    "numvend":"  MSL",
    "fecha":"2025-02-13",
    "fechacapt":"2025-02-13",
    "hora":"12:06:59",
    "divisa":"P",
    "tc":21.1,
    "importe":800.93,
    "descuento":45.83,
    "impuesto1":60.41,
    "impuesto2":0,
    "formapago":"1",
    "numalm":"38",
    "obs":"",
    "items":[
        {
            "tipodoc":" Q",
            "numdoc":"      OT79",
            "numpar":"  1",
            "numart":"              528790",
            "precio":34.25926,
            "pjedesc":0,
            "impuesto1":8,
            "impuesto2":0,
            "pend":6,
            "cant":6,
            "unidad":"PZA",
            "obs":""
        }
    ]
}
```

---

### Listar

GET /api/v3/cotizaciones?filters...

| Filtros | significado                                          | Tipo de filtro         | Uso                                                                       |
|---------|------------------------------------------------------|------------------------|---------------------------------------------------------------------------|
| offset  | A partir de que registro iniciar búsqueda. Default 0 | despues de             | desde que registro empezar: offset=10                                     |
| limit   | Cuantos registros obtener. Default 100               | cantidad               | cuantos registros recibir: limit=50                                       |
| q       | Palabras a buscar (numdoc, nomcli,nomcliev)          | busqueda por similitud | traer cotizaciones donde numdoc,numcli,nomcliev sean similares a q=AF134  |
| numalm  | clave de almacen                                     | busqueda por exactitud | traer cotizaciones cuando numalm=" 1"                                     |
| numuser | clave de usuario                                     | busqueda por exactitud | traer cotizaciones cuando numuser="  MSL"                                 |
| numcli  | clave de cliente                                     | busqueda por exactitud | traer cotizaciones cuando numcli="  B10" o numcliev="       B10"          |
| numvend | clave de vendedor                                    | busqueda por exactitud | traer cotizaciones cuando numvend="  MSL"                                 |
| fecha1  | traer todos los documentos mayores a fecha1          | busqueda mayor a       | traer cotizaciones con fecha de registro mayor a "2025-03-21"             |
| fecha2  | traer todos los documentos menores a fecha2          | busqueda menor a       | traer cotizaciones con fecha de registro menor a "2025-03-29"             |
| precio1 | traer todos los documentos con total mayor a precio1 | busqueda mayor a       | traer cotizaciones con total de venta mayor a 50 pesos                    |
| precio2 | traer todos los documentos con total menor a precio2 | busqueda menor a       | traer cotizaciones con total de venta menor a 250 pesos                   |
| order   |                                                      | ordenar cotizaciones   | ordenar cotizaciones segun updated,id,numdoc,fecha,numcli,numvend,numuser |

response:
```json
[
    {
        "id":2006081,
        "created":"2025-02-13 19:06:59",
        "updated":"2025-02-13 19:06:59",
        "tipodoc":" Q",
        "numdoc":"      OT79",
        "numcli":"    0",
        "numcliev":"          ",
        "numvend":"  MSL",
        "fecha":"2025-02-13",
        "fechacapt":"2025-02-13",
        "hora":"12:06:59",
        "divisa":"P",
        "tc":21.1,
        "importe":800.93,
        "descuento":45.83,
        "impuesto1":60.41,
        "impuesto2":0,
        "formapago":"1",
        "numalm":"38",
        "obs":"",
        "items":[
            {
                "tipodoc":" Q",
                "numdoc":"      OT79",
                "numpar":"  1",
                "numart":"              528790",
                "precio":34.25926,
                "pjedesc":0,
                "impuesto1":8,
                "impuesto2":0,
                "pend":6,
                "cant":6,
                "unidad":"PZA",
                "obs":""
            }
        ]
    },
    {}...
]
```
