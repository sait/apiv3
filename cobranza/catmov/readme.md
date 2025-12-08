## Catalogo de movimientos

Rutas de catalogo de movimientos

| Accion            | Ruta                             |
|-------------------|----------------------------------|
| [Leer](#leer)     | GET api/v3/cxc/catmov/:conc      |
| [Listar](#listar) | GET api/v3/cxc/catmov?filters... |

campos de catalogo de movimientos

| Campo       | Tipo        | Significado                                                 |
|-------------|-------------|-------------------------------------------------------------|
| conc        | CHAR(2)     | Clave del concepto                                          |
| desc        | VARCHAR(20) | Descripción del concepto                                    |
| ca          | CHAR(1)     | Indica si es cargo o abono                                  |
| obligaref   | TINYINT     | Si el documento debe llevar referencia                      |
| editar      | TINYINT     | Si es posible editar el concepto en la ventana de conceptos |
| reporte     | VARCHAR(60) | Nombre del reporte asignado                                 |
| sigfolio    | VARCHAR(10) | Número de folio que precede                                 |
| repetido    | TINYINT     | Permitir folios repetidos.                                  |
| clavesat    | CHAR(2)     | Clave SAT asociada al pago                                  |
| obligafolio | TINYINT     | Si obliga a usar folio al pago                              |

---
### Leer

GET /api/v3/cxc/catmov/FA

response:
```json
{
    "id": 1,
    "created": "2025-08-20 18:22:52",
    "updated": "2025-08-20 18:22:52",
    "conc": "FA",
    "desc": "FACTURA",
    "ca": "0",
    "editar": 1,
    "reporte": "",
    "sigfolio": "",
    "repetido": 0,
    "clavesat": "",
    "obligfolio": 0
}
```

---
### Listar

GET api/v3/cxc/catmov?filters

| Variable | Significado                                                          |
|----------|----------------------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0                 |
| limit    | Cuantos registros obtener. Default 100                               |
| order    | Orden deseado. Disponibles: id,created,updated,conc,ca,desc,sigfolio |
| q        | Palabras a buscar (conc,desc,reporte,clavesat)                       |

response:
```json
[
    {
      "id": 2,
      "created": "2025-08-20 18:22:52",
      "updated": "2025-08-20 18:22:52",
      "conc": "CD",
      "desc": "CHEQUE DEVUELTO",
      "ca": "0",
      "editar": 1,
      "reporte": "",
      "sigfolio": "",
      "repetido": 0,
      "clavesat": "02",
      "obligfolio": 0
    },
    {
      "id": 3,
      "created": "2025-08-20 18:22:52",
      "updated": "2025-08-20 18:22:52",
      "conc": "NC",
      "desc": "NOTA DE CREDITO",
      "ca": "1",
      "editar": 1,
      "reporte": "",
      "sigfolio": "",
      "repetido": 0,
      "clavesat": "",
      "obligfolio": 0
    },
    {}...
]
```
