## Bi Inventario

| Accion                                                                | Ruta                                                                                      |
|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| [Totales](#totales)                                                   | POST   /api/v3/bi_inventario/:pk/totales                                                  |
| [Stock actual](#stock-actual)                                         | POST   /api/v3/bi_inventario/:pk/stock_actual?limit=..&offset=..                          |
| [Valor por familia](#valor-por-familia)                               | POST   /api/v3/bi_inventario/:pk/valor_familia?limit=..&offset=..                         |
| [Top artículos vendidos](#top-articulos-vendidos)                     | 📅 POST   /api/v3/bi_inventario/:pk/top_articulos?ano=..&mes=..&limit=..&offset=..      |
| [Valor de inventario por sucursal](#valor-de-inventario-por-sucursal) | 📅 POST   /api/v3/bi_inventario/:pk/valor_sucursal?ano=..&mes=..&limit=..&offset=..     |
| [Índice de rotación](#indice-de-rotacion)                             | 📅 POST   /api/v3/bi_inventario/:pk/indice_rotacion?ano=..&mes=..                       |
| [Márgenes de ganancia](#margenes-de-ganancia)                         | 📅 POST   /api/v3/bi_inventario/:pk/margenes?ano=..&mes=..&limit=..&offset=..           |
| [Unidades vendidas por artículo](#unidades-vendidas-por-articulo)     | 📅 POST   /api/v3/bi_inventario/:pk/unidades_vendidas?ano=..&mes=..&limit=..&offset=..  |
| [Total de unidades vendidas](#total-de-unidades-vendidas)             | POST   /api/v3/bi_inventario/:pk/unidades_vendidas_total?fini=..&ffin=..                  |
| [Alertas de inventario](#alertas-de-inventario)                       | POST   /api/v3/bi_inventario/:pk/alertas?fini=..&ffin=..&tipo=..&limit=..&offset=..       |
| [Compras del periodo](#compras-del-periodo)                           | POST   /api/v3/bi_inventario/:pk/compras_periodo?fini=..&ffin=..&groupby=..&numalm=..&numprov=.. |

> `:pk` (obligatorio) actualmente no se usa para filtrar la consulta, pero es requerido por la ruta. Ejemplo: `1`.

---

### Conceptos generales

Cada ruta basada en movimientos (`minv`) usa su propio criterio de "venta":

| Ruta                | Movimientos que considera                                                                                |
|---------------------|----------------------------------------------------------------------------------------------------------|
| `top_articulos`     | `tipodoc = ' F'` (Factura) o `tipodoc = ' N'` (Nota de venta), sumando `abs(cant)`                       |
| `indice_rotacion`   | `tipodoc = ' F'` o `' N'` **y** `cant` negativo (salida de almacén)                                      |
| `margenes`          | Cualquier movimiento con `cant` negativo (salida de almacén), sin importar el `tipodoc`                  |
| `unidades_vendidas` | `tipodoc` `' F'`, `' N'`, `'DV'` y `'DN'` (ventas y sus devoluciones), venta neta: ventas menos devoluciones |
| `unidades_vendidas_total`, `alertas` | Igual que `top_articulos`: `tipodoc = ' F'` o `' N'`, sumando `abs(cant)`                 |

`limit`/`offset` no tienen un valor por defecto: si no se envían, la consulta regresa todos los registros sin límite. La excepción es [Unidades vendidas por artículo](#unidades-vendidas-por-articulo), que regresa 15 registros si no se envía `limit`.

Las rutas basadas en existencias (`totales`, `stock_actual`, `valor_familia`) usan `bi_inventario_existencias` (foto de `multialm`, que no tiene fecha): muestran el inventario del día de la última carga y no aceptan filtros de fecha.

---

### Filtros de fecha

Las rutas marcadas con 📅 filtran por fecha usando la tabla `fechas` (igual que `bi/ventas`). Es obligatorio enviar al menos uno de los siguientes parámetros:

| parametro | descripcion                                                                  |
|-----------|------------------------------------------------------------------------------|
| dia       | Día especificado (será necesario especificar mes y ano)                      |
| semana    | Semana especificada (será necesario especificar ano)                         |
| mes       | Mes especificado (será necesario especificar ano)                            |
| trimestre | Trimestre especificado (será necesario especificar ano)                      |
| semestre  | Semestre especificado (será necesario especificar ano)                       |
| ano       | Año especificado                                                             |
| fini      | Fecha inicial a considerar (20250101), debe enviarse junto con ffin          |
| ffin      | Fecha límite a considerar  (20251231), debe enviarse junto con fini          |

Ejemplos: `?ano=2025&mes=3`, `?ano=2025&trimestre=2`, `?fini=20250101&ffin=20250630`

Todas las combinaciones válidas de filtros forman un rango continuo de días. Por eso, excepto en [Márgenes de ganancia](#margenes-de-ganancia) (que hace el join con `fechas`), los filtros se resuelven primero contra `fechas` para obtener la fecha inicial, la final y los días, y las consultas filtran `id_fecha BETWEEN fecha inicial AND fecha final` sobre las [tablas BI](#tablas-bi). Si los filtros no encuentran fechas, la ruta regresa vacío (o `NULL` en índice de rotación).

---

### Tablas BI

Las rutas leen tablas pre-agregadas en lugar de `minv` y `multialm` directamente. Las tablas y su llenado estan en `database/dbtest/dashboard_inventario_propuesta.sql`. En las secciones de cada ruta, `minv` y `multialm` indican de donde salen los datos de esas tablas.

| Tabla                        | Se llena desde        | Rutas                                                              |
|------------------------------|-----------------------|--------------------------------------------------------------------|
| `bi_inventario_existencias`  | `multialm` + `arts`   | totales, stock_actual, valor_familia, indice_rotacion (inventario si no hay cortes), alertas (existencias) |
| `bi_inventario_mov`          | `minv` (dia, articulo, almacen) | top_articulos, margenes, unidades_vendidas, alertas (ventas) |
| `bi_inventario_mov_dia`      | `bi_inventario_mov` (dia, almacen) | valor_sucursal, indice_rotacion (costo de ventas), unidades_vendidas_total |
| `bi_inventario_existencias_hist` | `bi_inventario_existencias` (corte por fin de mes y almacen) | indice_rotacion (inventario promedio) |

> `bi_inventario_existencias` es una foto de `multialm` al momento de la carga, por lo que debe recargarse diario. Si no se recarga, las rutas de existencias regresan el inventario del último día cargado. Los movimientos de `minv` solo aparecen en las rutas después de recargar `bi_inventario_mov` y `bi_inventario_mov_dia`.

> Los importes de `bi_inventario_mov` se guardan con 4 decimales, por lo que las rutas de movimientos regresan los montos con 4 decimales.

---------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `totales`           | Sin cambios, recorre `multialm`                                                                                                           |
| `stock_actual`      | Join diferido: agrupa y pagina `multialm` y después une `arts` solo con los artículos de la página                                        |
| `valor_familia`     | Agrupa `multialm` por artículo antes de unir `arts` y `familias`                                                                          |
| `top_articulos`     | Con `limit`: `force index (tip_fec)` solo con `' F'`/`' N'` y join diferido a `arts`. Si la página no se llena con ventas > 0, usa la consulta completa por `fecha` (ver [Top artículos vendidos](#top-articulos-vendidos)) |
| `valor_sucursal`    | Obtiene los almacenes de `minv` leyendo solo el índice `alm_fec` y recorre cada almacén por rango de fecha con `force index (alm_fec)`; une `almacen` ya agrupado |
| `indice_rotacion`   | Costo de ventas con `force index (tip_fec)`                                                                                                |
| `margenes`          | Sin índice forzado, join con `fechas`                                                                                                      |
| `unidades_vendidas` | Join diferido: agrupa y pagina `minv` y después une `arts`                                                                                 |

---

### Totales

POST /api/v3/bi_inventario/:pk/totales

Regresa el resumen general del inventario: cantidad de artículos distintos, cantidad de almacenes distintos y el valor total de inventario (existencia x costo) en toda la empresa, a partir de la tabla `multialm`.

Campos
| Campo                  | Tipo  | Significado                                                                   |
|------------------------|-------|-------------------------------------------------------------------------------|
| total_numart           | int   | Cantidad de artículos distintos (`numart`) con registro en `multialm`         |
| total_numalm           | int   | Cantidad de almacenes distintos (`numalm`) con registro en `multialm`         |
| valor_total_inventario | float | Suma de `existencia x costopro` de todos los artículos en todos los almacenes |

Response
```json
{
  "result": {
    "total_numart": "3450",
    "total_numalm": "4",
    "valor_total_inventario": "8250430.5000"
  }
}
```

---

### Stock actual

POST /api/v3/bi_inventario/:pk/stock_actual?limit=..&offset=..

Regresa la existencia total por artículo, sumando la existencia de todos los almacenes/sucursales (tabla `multialm`), junto con su nombre/descripción (tabla `arts`), ordenado de mayor a menor existencia.

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |

Campos
| Campo            | Tipo   | Significado                                                                             |
|------------------|--------|-----------------------------------------------------------------------------------------|
| numart           | string | Número de identificación del artículo                                                   |
| nombre           | string | Descripción / nombre del artículo (`arts.desc`); `"Articulo sin nombre"` si viene vacío |
| existencia_total | float  | Suma de la existencia del artículo en todos los almacenes                               |

Response
```json
{
  "result": [
    { "numart": "  A102", "nombre": "TORNILLO 1/4 X 2", "existencia_total": "3200.0000" },
    { "numart": "  B045", "nombre": "TUERCA HEXAGONAL 1/2", "existencia_total": "2850.0000" },
    {}
  ]
}
```

---

### Valor por familia

POST /api/v3/bi_inventario/:pk/valor_familia?limit=..&offset=..

Regresa la existencia total agrupada por familia de artículo (`arts.familia`), tomando el nombre de familia desde el catálogo `familias`, ordenado de mayor a menor existencia.

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |

Campos
| Campo            | Tipo   | Significado                                                                                                                                      |
|------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| familia          | string | Número de identificación de la familia (`arts.familia`)                                                                                          |
| nombre           | string | Nombre de la familia (`familias.nomfam`); `"Sin familia"` si `familia` viene vacío, `"Familia sin nombre"` si no hay coincidencia en el catálogo |
| existencia_total | float  | Suma de la existencia de todos los artículos de esa familia (todos los almacenes)                                                                |

Response
```json
{
  "result": [
    { "familia": " 01", "nombre": "FERRETERIA", "existencia_total": "15420.0000" },
    { "familia": " 02", "nombre": "PLOMERIA", "existencia_total": "9830.0000" },
    { "familia": "", "nombre": "Sin familia", "existencia_total": "312.0000" },
    {}
  ]
}
```

---

### Top artículos vendidos

POST /api/v3/bi_inventario/:pk/top_articulos?ano=..&mes=..&limit=..&offset=..

Regresa los artículos con mayor cantidad vendida (ver [criterio de venta](#conceptos-generales)), junto con su nombre/descripción (tabla `arts`), ordenado de mayor a menor cantidad vendida.

También regresa con `cant_vend` en 0 los artículos que tuvieron movimientos de otro tipo en el periodo.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| 📅     | (obligatorio) [Filtros de fecha](#filtros-de-fecha) sobre `minv.fecha`         |
| limit  | (opcional) Cantidad de registros a tomar                                       |
| offset | (opcional) Tomar registros a partir del registro X                             |

Campos
| Campo     | Tipo   | Significado                                                                             |
|-----------|--------|-----------------------------------------------------------------------------------------|
| numart    | string | Número de identificación del artículo                                                   |
| nombre    | string | Descripción / nombre del artículo (`arts.desc`); `"Articulo sin nombre"` si viene vacío |
| cant_vend | float  | Suma de las unidades vendidas (`cant`, en valor absoluto) del artículo en el rango dado |

Response
```json
{
  "result": [
    { "numart": "  A102", "nombre": "TORNILLO 1/4 X 2", "cant_vend": "845.0000" },
    { "numart": "  B045", "nombre": "TUERCA HEXAGONAL 1/2", "cant_vend": "712.0000" },
    {}
  ]
}
```

---

### Valor de inventario por sucursal

POST /api/v3/bi_inventario/:pk/valor_sucursal?ano=..&mes=..&limit=..&offset=..

Regresa el valor de inventario (cantidad x costo) agrupado por almacén/sucursal, a partir de los movimientos de la tabla `minv` dentro del periodo, junto con el nombre del almacén (tabla `almacen`), ordenado por `numalm`.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| 📅     | (obligatorio) [Filtros de fecha](#filtros-de-fecha) sobre `minv.fecha`         |
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |

Campos
| Campo            | Tipo   | Significado                                                                  |
|------------------|--------|------------------------------------------------------------------------------|
| numalm           | string | Número de identificación del almacén/sucursal                                |
| nombre           | string | Nombre del almacén (`almacen.nomalm`); `"Almacen sin nombre"` si viene vacío |
| valor_inventario | float  | Suma de `cantidad x costo` de todos los artículos en ese almacén             |

Response
```json
{
  "result": [
    { "numalm": " 01", "nombre": "SUCURSAL CENTRO", "valor_inventario": "1250430.0000" },
    { "numalm": " 02", "nombre": "SUCURSAL NORTE", "valor_inventario": "685920.5000" },
    {}
  ]
}
```

---

### Índice de rotación

POST /api/v3/bi_inventario/:pk/indice_rotacion?ano=2025&mes=3

Calcula el índice de rotación de inventario y los días que tardaría en venderse el inventario promedio, dentro del periodo indicado con los [filtros de fecha](#filtros-de-fecha).

`indice_rotacion` = costo de ventas del periodo (ver [criterio de venta](#conceptos-generales)) / inventario promedio.
`dias_rotacion` = días del periodo entre `indice_rotacion`.

Si no hay ventas en el periodo, `costo_ventas` es 0 cuando hubo otros movimientos en `minv` y `NULL` cuando no hubo ningún movimiento.

> El rango (fecha inicial, fecha final y cantidad de días) se resuelve con la tabla `fechas` a partir de los filtros enviados. Reemplaza al parámetro `periodo=mes|anio`, que ya no se acepta.

> El inventario promedio es el promedio del valor total de inventario de los cortes de `bi_inventario_existencias_hist` (un corte por fin de mes, más el día de la última carga) desde el día anterior a la fecha inicial hasta la fecha final. Así se incluye el corte de fin del mes anterior como inventario inicial: para marzo se promedian los cortes del 28/29 de febrero y del 31 de marzo.
>
> Si no hay cortes en ese rango (por ejemplo, periodos anteriores a que se empezara a guardar el historial), se usa el valor **actual** del inventario (`bi_inventario_existencias`).

Campos
| Campo           | Tipo  | Significado                                                                                                                                                |
|-----------------|-------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| indice_rotacion | float | Costo de ventas del periodo entre el valor actual del inventario                                                                                           |
| dias_rotacion   | float | Días que tardaría en venderse todo el inventario actual al ritmo de venta del periodo (`días del periodo / indice_rotacion`)                                 |

Response
```json
{
  "result": {
    "indice_rotacion": "0.699117617070",
    "dias_rotacion": "522.0867"
  }
}
```

---

### Márgenes de ganancia

POST /api/v3/bi_inventario/:pk/margenes?ano=..&mes=..&limit=..&offset=..

Regresa ventas totales, costo total y ganancia por artículo (ver [criterio de venta](#conceptos-generales)), ordenado de mayor a menor ganancia. Agrupa `minv` por `numart` dentro del periodo: los artículos que solo tuvieron entradas regresan en 0.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| 📅     | (obligatorio) [Filtros de fecha](#filtros-de-fecha) sobre `minv.fecha`         |
| limit  | (opcional) Cantidad de registros a tomar                                       |
| offset | (opcional) Tomar registros a partir del registro X                             |

Campos
| Campo          | Tipo   | Significado                                                         |
|----------------|--------|---------------------------------------------------------------------|
| numart         | string | Número de identificación del artículo                               |
| ventas_totales | float  | Suma de `cantidad vendida x precio` del artículo en el rango dado   |
| costo_total    | float  | Suma de `cantidad vendida x costopro` del artículo en el rango dado |
| ganancia       | float  | `ventas_totales - costo_total`                                      |

Response
```json
{
  "result": [
    { "numart": "  A102", "ventas_totales": "4225.00", "costo_total": "2560.00", "ganancia": "1665.00" },
    { "numart": "  B045", "ventas_totales": "3560.00", "costo_total": "2136.00", "ganancia": "1424.00" },
    {}
  ]
}
```

---

### Unidades vendidas por artículo

POST /api/v3/bi_inventario/:pk/unidades_vendidas?ano=..&mes=..&limit=..&offset=..

Regresa las unidades y el monto vendido agrupados por artículo (ver [criterio de venta](#conceptos-generales)), junto con su descripción (tabla `arts`), ordenado por `cant_vendida` de mayor a menor.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| 📅     | (obligatorio) [Filtros de fecha](#filtros-de-fecha) sobre `minv.fecha`         |
| limit  | (opcional) Cantidad de registros a tomar, default 15                           |
| offset | (opcional) Tomar registros a partir del registro X                             |

Campos
| Campo         | Tipo   | Significado                                                                   |
|---------------|--------|-------------------------------------------------------------------------------|
| numart        | string | Número de identificación del artículo                                         |
| desc          | string | Descripción del artículo (`arts.desc`)                                        |
| cant_vendida  | float  | Unidades vendidas netas: facturas y notas (`' F'`,`' N'`) menos devoluciones (`DV`,`DN`) |
| monto_vendido | float  | Monto vendido neto (`precio x cant`) de los mismos movimientos                           |

> En `minv` las ventas tienen `cant` negativa y las devoluciones positiva, por eso se regresa `-sum(cant)` y `-sum(precio*cant)`: una venta suma en positivo y una devolución resta. Un artículo con más devoluciones que ventas en el periodo regresa valores negativos.

Response
```json
{
  "result": [
    { "numart": "  A102", "desc": "TORNILLO 1/4 X 2", "cant_vendida": "845.0000", "monto_vendido": "4225.0000" },
    { "numart": "  B045", "desc": "TUERCA HEXAGONAL 1/2", "cant_vendida": "712.0000", "monto_vendido": "3560.0000" },
    {}
  ]
}
```

---

### Rango fini-ffin

[Total de unidades vendidas](#total-de-unidades-vendidas) y [Alertas de inventario](#alertas-de-inventario) no usan los [filtros de fecha](#filtros-de-fecha) de la tabla `fechas`. Solo reciben un rango obligatorio:

| parametro | descripcion                                                                   |
|-----------|-------------------------------------------------------------------------------|
| fini      | (obligatorio) Fecha inicial, inclusiva, en formato `YYYYMMDD` (ej. `20260401`) |
| ffin      | (obligatorio) Fecha final, inclusiva, en formato `YYYYMMDD` (ej. `20260430`)   |

Ambas deben ser fechas reales (`20260231` no es válida) y `fini <= ffin`.

Errores:
| HTTP | Cuándo                                                                                          |
|------|-------------------------------------------------------------------------------------------------|
| 400  | `fini`/`ffin` faltantes o inválidas, `fini > ffin`, `tipo` desconocido, `limit`/`offset` inválidos |
| 500  | Falla la consulta en la base de datos                                                           |

```json
{ "error": "bi_inventario.alertas: el parametro fini debe ser una fecha valida en formato YYYYMMDD" }
```

---

### Total de unidades vendidas

POST /api/v3/bi_inventario/:pk/unidades_vendidas_total?fini=20260401&ffin=20260430

Regresa el total de unidades vendidas en el [rango](#rango-fini-ffin), con el mismo criterio que [Top artículos vendidos](#top-articulos-vendidos): suma `abs(cant)` de los movimientos de factura (`' F'`) y nota de venta (`' N'`) de todos los artículos. Conserva las cantidades fraccionarias y regresa `0` si no hubo ventas. Coincide con la suma de `cant_vend` de `top_articulos` sin paginar para el mismo rango.

Campos
| Campo                   | Tipo  | Significado                              |
|-------------------------|-------|------------------------------------------|
| total_unidades_vendidas | float | Unidades vendidas en el rango (inclusivo) |

Response
```json
{
  "result": {
    "total_unidades_vendidas": 1240
  }
}
```

---

### Alertas de inventario

POST /api/v3/bi_inventario/:pk/alertas?fini=20260401&ffin=20260430&tipo=..&limit=..&offset=..

Cruza la existencia **actual** de cada artículo (suma de todos los almacenes en `multialm`, igual que [Stock actual](#stock-actual)) con sus unidades vendidas en el [rango](#rango-fini-ffin) (mismo criterio que [Total de unidades vendidas](#total-de-unidades-vendidas)). Existencias y ventas se agrupan por artículo antes de cruzarse, para no multiplicar filas. Solo considera artículos con registro en `multialm`. Si un artículo no tuvo ventas en el rango, sus ventas cuentan como 0.

`dias_periodo` son los días calendario del rango, incluyendo ambos extremos.

Cada artículo queda como máximo en una alerta, evaluadas en este orden:

| tipo           | Condición                                                                                             |
|----------------|-------------------------------------------------------------------------------------------------------|
| stock_negativo | `existencia_total < 0`, sin importar las ventas                                                        |
| sin_ventas     | `existencia_total > 0` y `unidades_vendidas = 0`                                                       |
| cobertura_baja | `existencia_total >= 0`, `unidades_vendidas > 0` y cobertura **estrictamente menor a 14 días**         |

Cobertura = `existencia_total × dias_periodo ÷ unidades_vendidas`. La comparación contra 14 se hace con el valor sin redondear (una cobertura de exactamente 14 días no genera alerta). Con existencia 0 y ventas positivas, la cobertura es 0.

filtros
| Filtro    | Descripcion                                                                                           |
|-----------|-------------------------------------------------------------------------------------------------------|
| fini/ffin | (obligatorio) [Rango de fechas](#rango-fini-ffin)                                                     |
| tipo      | (opcional) `stock_negativo`, `sin_ventas` o `cobertura_baja`. Sin `tipo` regresa solo el resumen        |
| limit     | (opcional) Entero mayor a 0, cantidad de artículos del detalle. Default 10                             |
| offset    | (opcional) Entero mayor o igual a 0, tomar artículos a partir del registro X. Default 0                |

#### Resumen (sin tipo)

POST /api/v3/bi_inventario/1/alertas?fini=20260401&ffin=20260430

Cada conteo es de artículos distintos. Las alertas sin artículos regresan 0.

| Campo        | Tipo | Significado                                   |
|--------------|------|-----------------------------------------------|
| dias_periodo | int  | Días calendario del rango (incluye ambos extremos) |
| conteos      | obj  | Cantidad de artículos en cada tipo de alerta  |

Response
```json
{
  "result": {
    "dias_periodo": 30,
    "conteos": {
      "cobertura_baja": 18,
      "sin_ventas": 42,
      "stock_negativo": 3
    }
  }
}
```

#### Detalle (con tipo)

POST /api/v3/bi_inventario/1/alertas?fini=20260401&ffin=20260430&tipo=cobertura_baja&limit=10&offset=0

Detalle paginado de la alerta, ordenado por `numart`. `total` es la cantidad de artículos de la alerta antes de aplicar `limit` y `offset`. Una página fuera de rango regresa `articulos: []`.

| Campo                         | Tipo   | Significado                                                                                      |
|-------------------------------|--------|--------------------------------------------------------------------------------------------------|
| tipo                          | string | Tipo de alerta consultado                                                                        |
| total                         | int    | Artículos de la alerta sin paginar                                                               |
| limit / offset                | int    | Paginación aplicada                                                                              |
| articulos[].numart            | string | Número de identificación del artículo                                                            |
| articulos[].nombre            | string | Descripción del artículo (`arts.desc`); `"Articulo sin nombre"` si viene vacío o no existe en `arts` |
| articulos[].existencia_total  | float  | Existencia actual sumada en todos los almacenes                                                  |
| articulos[].unidades_vendidas | float  | Unidades vendidas en el rango                                                                    |
| articulos[].dias_cobertura    | float  | Cobertura redondeada a 2 decimales; `null` en `sin_ventas` y `stock_negativo`                    |

Response
```json
{
  "result": {
    "tipo": "cobertura_baja",
    "total": 18,
    "limit": 10,
    "offset": 0,
    "articulos": [
      {
        "numart": "A102",
        "nombre": "TORNILLO 1/4 X 2",
        "existencia_total": 20,
        "unidades_vendidas": 60,
        "dias_cobertura": 10
      }
    ]
  }
}
```

---

### Compras del periodo

POST /api/v3/bi_inventario/:pk/compras_periodo?fini=20260101&ffin=20260103&groupby=dia&numalm=1&numprov=123

Datos de la gráfica "Compras del periodo": importes de compras, devoluciones de compra y neto en MXN del rango `fini`-`ffin`, agrupados por calendario igual que "Ventas totales" (`GET /bi/ventas/empresa`). Incluye un resumen del rango y el neto del periodo anterior para el comparativo. Lee directamente la tabla `docum` (no usa tablas BI pre-agregadas), por lo que refleja los documentos al momento de la consulta.

filtros
| Filtro  | Descripcion                                                                                                      |
|---------|------------------------------------------------------------------------------------------------------------------|
| fini    | (obligatorio) Fecha inicial, inclusiva, `YYYYMMDD`. Mismas validaciones que el [rango fini-ffin](#rango-fini-ffin) |
| ffin    | (obligatorio) Fecha final, inclusiva, `YYYYMMDD`                                                                 |
| groupby | (opcional) `dia`, `semana`, `mes`, `trimestre`, `semestre` o `ano`. Default `mes`. Se escribe en minúsculas       |
| numalm  | (opcional) Almacén/sucursal. Se alinea a la derecha a 2 caracteres (`1` y `" 1"` son lo mismo)                    |
| numprov | (opcional) Proveedor. Se alinea a la derecha a 5 caracteres (`123` y `"  123"` son lo mismo)                      |

#### Documentos que se consideran

| Concepto     | Documentos de `docum`                                         |
|--------------|---------------------------------------------------------------|
| compras      | `tipodoc = ' C'` (compras)                                    |
| devoluciones | `tipodoc = 'DC'` (devoluciones de compra)                     |
| fuera        | `tipodoc = ' O'` (órdenes de compra) y cualquier otro tipodoc |
| fuera        | Documentos cancelados: `status = 1`. `status` vacío (`NULL`) u otro valor cuenta como vigente |

La fecha que se usa es `docum.fecha` (fecha de registro del documento, la misma de los filtros `fecha1`/`fecha2` de `GET /compras`).

#### Importes

- **Total del documento**: `importe + impuesto1 + impuesto2 - descuento`, igual que `GET /compras?totalizar=true`. Incluye impuestos y descuentos del documento; no resta `descuentog`.
- **Conversión a MXN**: si `divisa = 'P'` el total se toma tal cual; con cualquier otra divisa se multiplica por el tipo de cambio del documento (`tc`), igual que la totalización de compras.
- **Redondeo**: el total en MXN de cada documento se redondea a centavos (`ROUND(..., 2)` de MySQL: los medios centavos se redondean alejándose de cero) y desde ahí todas las sumas son exactas en decimal. Por eso el resumen es exactamente la suma de los intervalos.
- `compras` y `devoluciones` se regresan en positivo (las devoluciones se guardan en positivo en `docum`) y `neto = compras - devoluciones`, por lo que las devoluciones se restan una sola vez. El neto puede ser negativo.
- `documentos` cuenta las compras y devoluciones vigentes incluidas en los importes.

#### Agrupación

Los intervalos salen de la tabla `fechas`, con las mismas columnas que `GET /bi/ventas/empresa`:

| groupby   | Intervalo                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------|
| dia       | Un día                                                                                                           |
| semana    | `ano` + `semana`, donde `semana = WEEK(fecha)` de MySQL (empieza en domingo, de 0 a 53). La semana que cruza de diciembre a enero se parte en dos intervalos, uno por año (ej. `20251228-20251231` y `20260101-20260103`) |
| mes       | `ano` + `mes`                                                                                                    |
| trimestre | `ano` + `trimestre` (enero-marzo, abril-junio, julio-septiembre, octubre-diciembre)                               |
| semestre  | `ano` + `semestre` (enero-junio, julio-diciembre)                                                                |
| ano       | `ano`                                                                                                            |

- Regresa una fila por cada intervalo que toca el rango, en orden cronológico, incluyendo los intervalos sin movimientos (importes y documentos en `0`).
- `fini`/`ffin` de cada fila son los días que realmente se consideran: el primer y el último intervalo se recortan al rango solicitado. Los intervalos son continuos, sin huecos ni traslapes.
- La tabla `fechas` debe tener todos los días del rango; si no, la ruta regresa error 400 en lugar de omitir intervalos.

#### Periodo anterior

Es el rango inmediatamente anterior con la misma cantidad de días calendario: termina el día previo a `fini`. Ejemplos:

| Periodo consultado      | Periodo anterior        |
|-------------------------|-------------------------|
| `20260101` - `20260103` | `20251229` - `20251231` |
| `20240301` - `20240331` | `20240130` - `20240229` |
| `20260101` - `20261231` | `20250101` - `20251231` |

Usa los mismos filtros (`numalm`, `numprov`), tipos de documento, cancelaciones y reglas de importes. Solo regresa el neto: el porcentaje de cambio (y el caso de neto anterior en `0`) lo resuelve el frontend.

#### Errores

| HTTP | Cuándo                                                                                                    |
|------|-----------------------------------------------------------------------------------------------------------|
| 400  | `fini`/`ffin` faltantes o inválidas, `fini > ffin`, `groupby` no válido, rango fuera de la tabla `fechas` |
| 500  | Falla la consulta en la base de datos                                                                     |

```json
{ "error": "bi_inventario.comprasPeriodo: groupby 'quincena' no valido, opciones: dia, semana, mes, trimestre, semestre, ano" }
```

#### Respuesta

Campos
| Campo                    | Tipo   | Significado                                                       |
|--------------------------|--------|-------------------------------------------------------------------|
| moneda                   | string | Siempre `MXN`                                                     |
| agrupacion               | string | `groupby` aplicado                                                |
| periodo.fini / ffin      | string | Rango consultado, `YYYYMMDD`                                      |
| resumen                  | objeto | Suma de todos los intervalos (mismos campos que cada intervalo, sin fechas) |
| periodo_anterior.fini / ffin | string | Rango anterior, `YYYYMMDD`                                    |
| periodo_anterior.neto    | number | Neto del rango anterior                                           |
| periodos[].fini / ffin   | string | Días considerados del intervalo, `YYYYMMDD`                       |
| periodos[].compras       | number | Compras en MXN                                                    |
| periodos[].devoluciones  | number | Devoluciones de compra en MXN, en positivo                        |
| periodos[].neto          | number | `compras - devoluciones`                                          |
| periodos[].documentos    | int    | Compras y devoluciones vigentes del intervalo                     |

Los importes son números con hasta 2 decimales. Cada elemento de `periodos` es una barra de la gráfica y la estructura es la misma para todas las agrupaciones.

POST /api/v3/bi_inventario/1/compras_periodo?fini=20260101&ffin=20260103&groupby=dia
```json
{
  "result": {
    "moneda": "MXN",
    "agrupacion": "dia",
    "periodo": { "fini": "20260101", "ffin": "20260103" },
    "resumen": { "compras": 6000, "devoluciones": 300, "neto": 5700, "documentos": 8 },
    "periodo_anterior": { "fini": "20251229", "ffin": "20251231", "neto": 5000 },
    "periodos": [
      { "fini": "20260101", "ffin": "20260101", "compras": 2000, "devoluciones": 100, "neto": 1900, "documentos": 3 },
      { "fini": "20260102", "ffin": "20260102", "compras": 2500, "devoluciones": 0, "neto": 2500, "documentos": 2 },
      { "fini": "20260103", "ffin": "20260103", "compras": 1500, "devoluciones": 200, "neto": 1300, "documentos": 3 }
    ]
  }
}
```

POST /api/v3/bi_inventario/1/compras_periodo?fini=20251215&ffin=20260210&groupby=mes
```json
{
  "result": {
    "moneda": "MXN",
    "agrupacion": "mes",
    "periodo": { "fini": "20251215", "ffin": "20260210" },
    "resumen": { "compras": 4878.88, "devoluciones": 3316, "neto": 1562.88, "documentos": 12 },
    "periodo_anterior": { "fini": "20251018", "ffin": "20251214", "neto": 0 },
    "periodos": [
      { "fini": "20251215", "ffin": "20251231", "compras": 1499, "devoluciones": 200, "neto": 1299, "documentos": 3 },
      { "fini": "20260101", "ffin": "20260131", "compras": 3379.88, "devoluciones": 3116, "neto": 263.88, "documentos": 9 },
      { "fini": "20260201", "ffin": "20260210", "compras": 0, "devoluciones": 0, "neto": 0, "documentos": 0 }
    ]
  }
}
```

#### Rendimiento

- `docum` se filtra por `tipodoc IN (' C','DC')` y `fecha BETWEEN` sobre la columna sin funciones, para que MySQL pueda usar los index `tip_fec` / `alm_tip_fec` / `fecha`. No se fuerza un index porque no todas las bases lo tienen.
- Los documentos se agregan por día antes de cruzarse con `fechas` (una fila por fecha en cada lado), por lo que el join no duplica documentos. `fechas` se filtra por su llave primaria (`id` = `YYYYMMDD`).
- El periodo anterior es una sola consulta de suma sobre `docum` con el mismo filtro.

#### Pruebas

`bi/inventario/compras_periodo_test.go`. Las pruebas de integración crean una base de datos temporal y requieren `SAITNUBE_TEST_DSN`:

```sh
cd src
go test ./bi/inventario/ -run 'Importe|RangoAnterior|ComprasPeriodo|FiltroCompras' -v
SAITNUBE_TEST_DSN='usuario:password@tcp(127.0.0.1:3306)/' go test ./bi/inventario/ -run ComprasPeriodo -v
```
