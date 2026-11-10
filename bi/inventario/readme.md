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

Las rutas basadas en existencias (`totales`, `stock_actual`, `valor_familia`) usan la tabla `multialm`, que no tiene fecha: siempre muestran la foto actual del inventario y no aceptan filtros de fecha.

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

Todas las combinaciones válidas de filtros forman un rango continuo de días. Por eso, excepto en [Márgenes de ganancia](#margenes-de-ganancia) (que hace el join con `fechas`), los filtros se resuelven primero contra `fechas` para obtener la fecha inicial, la final y los días, y las consultas filtran `minv.fecha BETWEEN fecha inicial AND fecha final`, lo que permite usar los índices de fecha de `minv`. Si los filtros no encuentran fechas, la ruta regresa vacío (o `NULL` en índice de rotación).

---

### Índices utilizados

| Ruta                | Optimización                                                                                                                              |
|---------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
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

También regresa con `cant_vend` en 0 los artículos que tuvieron movimientos de otro tipo en el periodo. Cuando se envía `limit` primero se consultan solo facturas y notas; si la página no se llena o su último registro tiene `cant_vend` en 0, se ejecuta la consulta completa para incluir esos ceros.

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

Calcula el índice de rotación de inventario y los días que tardaría en venderse todo el inventario actual, dentro del periodo indicado con los [filtros de fecha](#filtros-de-fecha).

`indice_rotacion` = costo de ventas del periodo (ver [criterio de venta](#conceptos-generales)) / valor actual del inventario.
`dias_rotacion` = días del periodo entre `indice_rotacion`.

Si no hay ventas en el periodo, `costo_ventas` es 0 cuando hubo otros movimientos en `minv` y `NULL` cuando no hubo ningún movimiento.

> El rango (fecha inicial, fecha final y cantidad de días) se resuelve con la tabla `fechas` a partir de los filtros enviados. Reemplaza al parámetro `periodo=mes|anio`, que ya no se acepta.

> El inventario se toma como el valor **actual** de existencias (`multialm`), no un promedio histórico entre inicio y fin del periodo, ya que no se lleva snapshot histórico de existencias.

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
