## Remisiones

Rutas de remisiones (documentos `tipodoc=' R'` de la tabla `docum`)

| Accion                            | Ruta                                               |
|-----------------------------------|----------------------------------------------------|
| [Leer por clave](#leer-por-clave) | GET   /api/v3/remisiones/:numdoc                   |
| [Listar](#listar)                 | GET   /api/v3/remisiones?filters...                |
| [Totales](#totales-de-remisiones) | GET   /api/v3/remisiones?totalizar=true&filters... |

> Las rutas `POST /api/v3/remisiones` y `POST /api/v3/remisiones/:numdoc/:accion` quedan registradas
> por `core.DocHandler`, pero `Create` y `Action` aun no estan implementados.

---
### Leer por Clave

GET /api/v3/remisiones/:numdoc

Regresa el encabezado de la remision con sus partidas (cada una con su articulo), ademas del cliente, almacen y vendedor.
Regresa 404 si la remision no existe.

response:
```json
{
    "id": 2005590,
    "created": "2024-10-10 10:12:45",
    "updated": "2024-10-10 10:12:45",
    "tipodoc": " R",
    "numdoc": "      R150",
    "fecha": "2024-10-10",
    "hora": "10:12:45",
    "numcli": "  B10",
    "nomcli": "",
    "numcliev": "",
    "numvend": "  MSL",
    "numalm": " 1",
    "mostrador": "",
    "divisa": "P",
    "moneda": "",
    "tc": 16.35,
    "importe": 14.81,
    "pjedesc": 0,
    "descuento": 0,
    "impuesto1": 1.19,
    "impuesto2": 0,
    "costo": 8.79,
    "costo2": 0,
    "costopro": 0,
    "total": 0,
    "obs": "",
    "items": [
      {
        "tipodoc": " R",
        "numdoc": "      R150",
        "numpar": "1",
        "cant": 2,
        "numart": "              IMPACC",
        "unidad": "PZA",
        "precio": 7.40741,
        "impuesto1": 8,
        "impuesto2": 0,
        "pjedesc": 0,
        "pjedesc2": 0,
        "obs": "",
        "articulo": {
          "numart": "              IMPACC",
          "desc": "IMPRESION A COLOR",
          ...
        }
      }
    ],
    "uuid": "",
    "datoscfdi": "",
    "fueticket": 0,
    "usocfdi": "",
    "metodopagosat": "",
    "formapagosat": "",
    "formapago": "1",
    "condicionesdepago": "",
    "cfdirelacionados": "",
    "tiporelacion": "",
    "status": "",
    "cliente": {
      "numcli": "  B10",
      "nomcli": "Bimbo",
      ...
    },
    "almacen": {
      "numalm": " 1",
      "nomalm": "Matriz",
      ...
    },
    "vendedor": {
      "numvend": "  MSL",
      "nomvend": "Microsistemas San Luis",
      ...
    }
}
```

---
### Listar

GET /api/v3/remisiones?filters...

Regresa los encabezados de las remisiones (sin partidas). Si no se envia `limit` se regresan todos los registros.

| Filtros   | significado                                                   | Tipo de filtro         | Uso                                                                        |
|-----------|---------------------------------------------------------------|------------------------|----------------------------------------------------------------------------|
| offset    | A partir del registro a iniciar                               | despues de             | desde que registro empezar: offset=10                                      |
| limit     | Cuantos registros obtener                                     | cantidad               | cuantos registros recibir: limit=50                                        |
| q         | Palabras a buscar (numdoc, numcli, nomcli, numcliev, nomcliev) | busqueda por similitud | traer remisiones donde numdoc,numcli,nomcli sean similares a q=R134        |
| numdoc    | clave de remision                                             | busqueda por exactitud | traer remisiones cuando numdoc="R150"                                      |
| numalm    | clave de almacen                                              | busqueda por exactitud | traer remisiones cuando numalm=" 1"                                        |
| numuser   | clave de usuario                                              | busqueda por exactitud | traer remisiones cuando numuser="  MSL"                                    |
| numcli    | clave de cliente                                              | busqueda por exactitud | traer remisiones cuando numcli="  B10"; si contiene "-" filtra por numcliev |
| numvend   | clave de vendedor                                             | busqueda por exactitud | traer remisiones cuando numvend="  MSL"                                    |
| numzona   | zona del cliente                                              | busqueda por exactitud | traer remisiones de clientes con numzona="1"                               |
| clasif    | clasificacion del cliente                                     | busqueda por exactitud | traer remisiones de clientes con clasif="A"                                |
| tipofact  | tipo de remision                                              | busqueda por exactitud | 1=Normales, 2=Remision diaria, 3=De ticket, 4=De ticket en nube, omitir=todas |
| status    | status de la remision                                         | busqueda por exactitud | 1=canceladas, ""=todas las remisiones                                      |
| divisa    | divisa de la remision                                         | busqueda por exactitud | P=MXN, D=USD                                                               |
| fecha1    | traer las remisiones con fecha mayor o igual a fecha1         | busqueda mayor a       | traer remisiones con fecha de registro mayor a "2025-03-21"                |
| fecha2    | traer las remisiones con fecha menor o igual a fecha2         | busqueda menor a       | traer remisiones con fecha de registro menor a "2025-03-29"                |
| totalizar | regresar totales en lugar del listado                         | totales                | ver [Totales de remisiones](#totales-de-remisiones)                        |

Response
```json
[
  {
    "id": 2283740,
    "created": "2024-07-10 08:51:23",
    "updated": "2026-01-06 14:30:33",
    "tipodoc": " R",
    "numdoc": "      R150",
    "status": "",
    "tc": 17.4,
    "fecha": "2024-07-10",
    "divisa": "P",
    "moneda": "MXN",
    "importe": 4.17,
    "impuesto1": 0.33,
    "impuesto2": 0,
    "descuento": 0,
    "total": 4.5,
    "nomcli": "Bimbo",
    "numcli": "  B10"
  }
]
```

---
### Totales de remisiones

Totales agrupados por Divisa, en el caso de la moneda "ZZZ" se refiere a la suma de los totales de todas las divisas convertidas a pesos mexicanos

GET /api/v3/remisiones?totalizar=true&filters...

Acepta los mismos filtros que [Listar](#listar), excepto `limit` y `offset`.
Si se envia `onlyitems=true` solo regresa la cantidad de remisiones: `{"items": 1203}`.

Response
```json
{
  "total": {
    "descuento": 89865.61,
    "importe": 149012052.78,
    "impuesto1": 11514039.56,
    "impuesto2": 2372.31,
    "items": 0,
    "moneda": "ZZZ",
    "total": 160438599.03
  },
  "totalesmoneda": [
    {
      "descuento": 314.15,
      "importe": 3915285.33,
      "impuesto1": 300302.58,
      "impuesto2": 7.33,
      "items": 0,
      "moneda": "DLS",
      "total": 4215281.09
    },
    {
      "descuento": 84603.6,
      "importe": 141126582.44,
      "impuesto1": 10885026.02,
      "impuesto2": 2372.31,
      "items": 0,
      "moneda": "MXN",
      "total": 151929377.17
    }
  ]
}
```
