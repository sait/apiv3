## Conccxp (Conceptos de compras por proveedor)

| Accion                  | Ruta                             |
|-------------------------|----------------------------------|
| [Consultar](#consultar) | GET   /api/v3/conccxp/:pk        |
| [Listar](#listar)       | GET   /api/v3/conccxp?filtros... |

> `:pk` corresponde a la clave de concepto (`conc`). Se alinea internamente a 2 caracteres en todas las rutas.

> Pendientes de implementar: `Create`, `Update`, `Delete` (por ahora regresan el registro/`nil` sin hacer ningún cambio).

---

### Conceptos generales

Campos del registro (tabla `conccxp`)
| Campo     | Tipo   | Significado                                           |
|-----------|--------|-------------------------------------------------------|
| id        | int    | Identificador autoincrementable interno               |
| created   | string | Fecha de creación del registro                        |
| updated   | string | Fecha de la última modificación                       |
| conc      | string | Llave primaria (PK). Clave del concepto, 2 caracteres |
| desc      | string | Descripción del concepto                              |
| ca        | string | Tipo de movimiento: `'0'` cargo, `'1'` abono          |
| obligaref | bool   | Si el movimiento obliga a capturar referencia         |
| editar    | bool   | Si el concepto permite editarse                       |
| reporte   | string | Nombre/ruta del reporte asociado al concepto          |
| sigfolio  | string | Siguiente folio disponible para este concepto         |

> Todos los campos, salvo `id` y `conc`, son *nullable*: si el concepto no tiene ese dato capturado, la API regresa `null` en vez de una cadena vacía.

---

### Consultar

GET /api/v3/conccxp/:pk

Obtiene un concepto por su clave (`conc`). Regresa el registro completo con todos los campos de la tabla `conccxp` (equivale a `SELECT * FROM conccxp WHERE conc = :pk`).

`:pk` (obligatorio) corresponde a la clave del concepto a consultar.

Response
```json
{
  "id": 12,
  "created": "2024-03-10 09:15:00",
  "updated": "2024-03-10 09:15:00",
  "conc": "CO",
  "desc": "COMPRA",
  "ca": "0",
  "obligaref": false,
  "editar": true,
  "reporte": null,
  "sigfolio": null
}
```

---

### Listar

GET /api/v3/conccxp?filtros...

Retorna los conceptos aplicando filtros opcionales. Solo trae las columnas `id, conc, desc, ca, obligaref, editar, reporte, sigfolio` (no trae `created`/`updated`, a diferencia de [Consultar](#consultar)). El resultado se ordena por `conc`.

filtros
| Filtro | Descripcion                                                                                                                         |
|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| q      | Busqueda de texto libre sobre `conc` y `desc`. Se separa por espacios (hasta 4 palabras) y cada palabra debe coincidir parcialmente |
| limit  | Cantidad de registros a tomar                                                                                                       |
| offset | Tomar registros a partir del registro X                                                                                             |

Response
```json
[
  {
    "id": "12",
    "conc": "CO",
    "desc": "COMPRA",
    "ca": "0",
    "obligaref": "0",
    "editar": "1",
    "reporte": null,
    "sigfolio": null
  },
  {
    "id": "13",
    "conc": "DC",
    "desc": "DEVOLUCION COMPRA",
    "ca": "1",
    "obligaref": "1",
    "editar": "1",
    "reporte": null,
    "sigfolio": null
  },
  {}
]
```