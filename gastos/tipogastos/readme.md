## Tipo Gastos

| Accion                  | Ruta                                  |
|-------------------------|---------------------------------------|
| [Consultar](#consultar) | GET   /api/v3/tipos_gastos/:idregla   |
| [Listar](#listar)       | GET   /api/v3/tipos_gastos?filtros... |

> Nota: este módulo filtra siempre por `modulo=4` (catálogo de tipos de gasto), tanto en `Listar` como implícitamente en la tabla `reglas1`.

---

### Conceptos generales

Campos del registro (tabla `reglas1`)
| Campo     | Tipo   | Significado                                                        |
|-----------|--------|--------------------------------------------------------------------|
| id        | int    | Identificador interno autoincremental del registro                 |
| created   | string | Fecha de creación del registro                                     |
| updated   | string | Fecha de la última actualización del registro                      |
| idregla   | int    | Identificador único del tipo de gasto (PK del módulo)              |
| tiporegla | int    | Tipo de regla asociado (clasificación interna)                     |
| numprov   | string | Número de proveedor asociado a la regla, si aplica                 |
| desc      | string | Descripción / nombre del tipo de gasto                             |
| modulo    | int    | Módulo al que pertenece la regla (siempre `4` para tipos de gasto) |
| tipodoc   | string | Tipo de documento asociado a la regla                              |
| condicion | string | Condición de aplicación de la regla                                |
| datoscapt | string | Datos de captura adicionales configurados para la regla            |

---

### Consultar

GET /api/v3/tipogastos/:idregla

Obtiene un tipo de gasto por su identificador (`idregla`).

`:idregla` (obligatorio) corresponde al identificador único del tipo de gasto a consultar.

Response
```json
{
  "id": 12,
  "created": "2024-03-10 09:15:00",
  "updated": "2024-03-10 09:15:00",
  "idregla": 7,
  "tiporegla": 1,
  "numprov": "  123",
  "desc": "GASOLINA",
  "modulo": 4,
  "tipodoc": " G",
  "condicion": "",
  "datoscapt": ""
}
```

---

### Listar

GET /api/v3/tipogastos?filtros...

Retorna los tipos de gasto (`modulo=4`) aplicando filtros opcionales. Solo trae las columnas `id, created, updated, idregla, desc, modulo, tiporegla` (no trae todos los campos del registro, a diferencia de [Consultar](#consultar)).

filtros
| Filtro | Descripcion                                                                                                                |
|--------|----------------------------------------------------------------------------------------------------------------------------|
| q      | Busqueda de texto libre sobre `desc`. Se separa por espacios (hasta 4 palabras) y cada palabra debe coincidir parcialmente |
| limit  | Cantidad de registros a tomar                                                                                              |
| offset | Tomar registros a partir del registro X                                                                                    |

Response
```json
[
  {
    "id": "12",
    "created": "2024-03-10 09:15:00",
    "updated": "2024-03-10 09:15:00",
    "idregla": "7",
    "desc": "GASOLINA",
    "modulo": "4",
    "tiporegla": "1"
  },
  {}
]
```