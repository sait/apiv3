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
### Crear pedido

POST  /api/v3/pedidos

```
Nota: al enviar el parametro "dryrun=true" se prevalidaran los datos sin afectar los campos en la base de datos
ejemplo: /api/v3/pedidos?dryrun=true

response: OK
```


**body** 
| campo     | descrip                                    |
|-----------|--------------------------------------------|
| numcli    | Numero de identificacion del cliente       |
| numalm    | Numero de almacen                          |
| formapago | 1: Contado - 2: Credito                    |
| divisa    | P:pesos - D:dolares                        |
| fentrega  | fecha de entrega (YEARMONTHDAY-"20251220") |
| hentrega  | Hora de entrega (HORA:MINUTOS-"18:30")     |
| items     | Items del pedido                           |

**body items**
| campo   | descrip                               |
|---------|---------------------------------------|
| cant    | cantidad de unidades del articulo     |
| numart  | numero de identificacion del articulo |
| unidad  | unidad del articulo                   |
| precio  | P:pesos - D:dolares                   |
| pjedesc | porcentaje de descuento a aplicar     |

request:
```json
{
    "numcli": "0",
    "numalm": "1",
    "formapago": "1",
    "divisa": "P", 
    "items": [
        {
            "cant": 1,
            "numart": "0164",
            "unidad": "PZA",
            "precio": 25,
            "pjedesc": 0
        }
    ],
    "fentrega": "20251220",
    "hentrega": "18:30"
}
```

response:
```json
{
    "id": 3478584,
    "created": "2025-12-05 11:18:04",
    "updated": "2025-12-05 11:18:04",
    "tipodoc": " P",
    "numdoc": "       AT3",
    "numcli": "    0",
    "nomcli": "C O N T A D O",
    "numvend": "     ",
    "pagos": "",
    "fecha": "2025-12-05",
    "fechacapt": "2025-12-05",
    "hora": "12:18:04",
    "fentrega": "2025-12-20",
    "hentrega": "18:30",
    "divisa": "P",
    "tc": 22,
    "importe": 25,
    "descuento": 0,
    "impuesto1": 2,
    "impuesto2": 0,
    "formapago": "1",
    "numalm": " 1",
    "obs": "",
    "total": 27,
    "items": [
      {
        "tipodoc": " P",
        "numdoc": "       AT3",
        "numpar": "  1",
        "numart": "                0164",
        "desc": "CUADERNO ESTRELLA ITALIANO ESPIRAL DOBLE RAYA C/100 HOJAS",
        "codigo": "        602760001640",
        "precio": 25,
        "pjedesc": 0,
        "impuesto1": 8,
        "impuesto2": 0,
        "pend": 1,
        "cant": 1,
        "unidad": "PZA",
        "obs": "",
        "foto": "",
        "fotos": "",
        "imagenes": null,
        "articulo": null,
        "preciopub": 0,
        "pjedesc1": 0
      }
    ],
    "cliente": null,
    "almacen": null,
    "vendedor": null,
    "direnvio": "",
    "otrosdatos": "",
    "clievent": null
  }
```


---
### Leer pedido

GET /api/v3/pedidos/:numdoc

response:
```json
{
    "id": 3478584,
    "created": "2025-12-05 11:18:04",
    "updated": "2025-12-05 11:18:04",
    "tipodoc": " P",
    "numdoc": "       AT3",
    "numcli": "    0",
    "nomcli": "C O N T A D O",
    "numvend": "     ",
    "pagos": "",
    "fecha": "2025-12-05",
    "fechacapt": "2025-12-05",
    "hora": "12:18:04",
    "fentrega": "2025-12-20",
    "hentrega": "18:30",
    "divisa": "P",
    "tc": 22,
    "importe": 25,
    "descuento": 0,
    "impuesto1": 2,
    "impuesto2": 0,
    "formapago": "1",
    "numalm": " 1",
    "obs": "",
    "total": 27,
    "items": [
      {
        "tipodoc": " P",
        "numdoc": "       AT3",
        "numpar": "  1",
        "numart": "                0164",
        "desc": "CUADERNO ESTRELLA ITALIANO ESPIRAL DOBLE RAYA C/100 HOJAS",
        "codigo": "        602760001640",
        "precio": 25,
        "pjedesc": 0,
        "impuesto1": 8,
        "impuesto2": 0,
        "pend": 1,
        "cant": 1,
        "unidad": "PZA",
        "obs": "",
        "foto": "",
        "fotos": "",
        "imagenes": null,
        "articulo": null,
        "preciopub": 0,
        "pjedesc1": 0
      }
    ],
    "cliente": null,
    "almacen": null,
    "vendedor": null,
    "direnvio": "",
    "otrosdatos": "",
    "clievent": null
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
[
    {
    "id": 3478584,
    "created": "2025-12-05 11:18:04",
    "updated": "2025-12-05 11:18:04",
    "tipodoc": " P",
    "numdoc": "       AT3",
    "numcli": "    0",
    "nomcli": "C O N T A D O",
    "numvend": "     ",
    "pagos": "",
    "fecha": "2025-12-05",
    "fechacapt": "2025-12-05",
    "hora": "12:18:04",
    "fentrega": "2025-12-20",
    "hentrega": "18:30",
    "divisa": "P",
    "tc": 22,
    "importe": 25,
    "descuento": 0,
    "impuesto1": 2,
    "impuesto2": 0,
    "formapago": "1",
    "numalm": " 1",
    "obs": "",
    "total": 27,
    "items": [
      {
        "tipodoc": " P",
        "numdoc": "       AT3",
        "numpar": "  1",
        "numart": "                0164",
        "desc": "CUADERNO ESTRELLA ITALIANO ESPIRAL DOBLE RAYA C/100 HOJAS",
        "codigo": "        602760001640",
        "precio": 25,
        "pjedesc": 0,
        "impuesto1": 8,
        "impuesto2": 0,
        "pend": 1,
        "cant": 1,
        "unidad": "PZA",
        "obs": "",
        "foto": "",
        "fotos": "",
        "imagenes": null,
        "articulo": null,
        "preciopub": 0,
        "pjedesc1": 0
      }
    ],
    "cliente": null,
    "almacen": null,
    "vendedor": null,
    "direnvio": "",
    "otrosdatos": "",
    "clievent": null
  },
  {}...
]
```

