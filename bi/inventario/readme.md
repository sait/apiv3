## Bi Inventario

| Accion                                                                | Ruta                                                                                      |
|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| [Totales](#totales)                                                   | POST   /api/v3/bi_inventario/:pk/totales                                                  |
| [Stock actual](#stock-actual)                                         | POST   /api/v3/bi_inventario/:pk/stock_actual?limit=..&offset=..&numalm=..                |
| [Valor por familia](#valor-por-familia)                               | POST   /api/v3/bi_inventario/:pk/valor_familia?limit=..&offset=..&numalm=..               |
| [Top artículos vendidos](#top-articulos-vendidos)                     | 📅 POST   /api/v3/bi_inventario/:pk/top_articulos?ano=..&mes=..&limit=..&offset=..      |
| [Valor de inventario por sucursal](#valor-de-inventario-por-sucursal) | 📅 POST   /api/v3/bi_inventario/:pk/valor_sucursal?ano=..&mes=..&limit=..&offset=..     |
| [Índice de rotación](#indice-de-rotacion)                             | 📅 POST   /api/v3/bi_inventario/:pk/indice_rotacion?ano=..&mes=..                       |
| [Márgenes de ganancia](#margenes-de-ganancia)                         | 📅 POST   /api/v3/bi_inventario/:pk/margenes?ano=..&mes=..&limit=..&offset=..           |
| [Unidades vendidas por artículo](#unidades-vendidas-por-articulo)     | 📅 POST   /api/v3/bi_inventario/:pk/unidades_vendidas?ano=..&mes=..&limit=..&offset=..  |
| [Total de unidades vendidas](#total-de-unidades-vendidas)             | POST   /api/v3/bi_inventario/:pk/unidades_vendidas_total?fini=..&ffin=..                  |
| [Alertas de inventario](#alertas-de-inventario)                       | POST   /api/v3/bi_inventario/:pk/alertas?fini=..&ffin=..&tipo=..&limit=..&offset=..       |
| [Compras del periodo](#compras-del-periodo)                           | POST   /api/v3/bi_inventario/:pk/compras_periodo?fini=..&ffin=..&groupby=..&numalm=..&numprov=.. |
| [Existencias fuera de límites](#existencias-fuera-de-limites)          | POST   /api/v3/bi_inventario/:pk/reposicion?limit=..&offset=..&estado=..&numalm=..&numart=..&q=..&incluir_sin_limites=.. |

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

POST /api/v3/bi_inventario/:pk/stock_actual?limit=..&offset=..&numalm=..

Regresa la existencia total por artículo, sumando la existencia de todos los almacenes/sucursales (o solo de `numalm`) de `bi_inventario_existencias` (foto de `multialm` de la última carga), junto con su nombre/descripción (tabla `arts`), ordenado de mayor a menor existencia y, en empates, por `numart` (paginación determinista).

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |
| numalm | (opcional) Almacén/sucursal. Ver [Filtro numalm](#filtro-numalm) |

#### Filtro numalm

`numalm` es el código de almacén/sucursal, igual que en las demás rutas:

- Omitido, vacío o solo espacios: todas las sucursales (comportamiento anterior).
- Es texto, no número: se quitan espacios y se alinea a la derecha a 2 caracteres como en SAIT (`2`, ` 2` y `2 ` consultan `" 2"`; `02` es otro almacén). Más de 2 caracteres: HTTP 400 `numalm debe tener maximo 2 caracteres`.
- Un código válido sin existencias regresa la respuesta vacía de siempre (`"result": null`), nunca todas las sucursales.
- Se aplica con `WHERE numalm = ?` sobre `bi_inventario_existencias` **antes** de `SUM`, `GROUP BY`, `ORDER BY`, `LIMIT` y `OFFSET`: la suma y la paginación son solo de esa sucursal. La tabla guarda una fila por artículo y almacén (foto de `multialm`), así que el filtro usa la misma foto que la consulta sin `numalm`.

Campos
| Campo            | Tipo   | Significado                                                                             |
|------------------|--------|-----------------------------------------------------------------------------------------|
| numart           | string | Número de identificación del artículo, alineado como en SAIT                            |
| nombre           | string | Descripción / nombre del artículo (`arts.desc`); `"Articulo sin nombre"` si viene vacío. `""` si el artículo no existe en `arts` |
| existencia_total | string | Suma de la existencia del artículo en los almacenes consultados (decimal como texto). `""` si todas sus existencias son `NULL` |

Response real (foto cargada desde la base local)

POST /api/v3/bi_inventario/1/stock_actual?limit=5&offset=0
```json
{"result":[
  {"existencia_total":"24246.000","nombre":"MICRO SPRAY 360°, 110LPH","numart":"               SJ360"},
  {"existencia_total":"15895.000","nombre":"PIJAS HEXAGONAL, PUNTA DE BROCA 14-3\"","numart":"               81211"},
  {"existencia_total":"10821.780","nombre":"PTR 11/2\" CALIBRE 14","numart":"             C-15012"},
  {"existencia_total":"6475.000","nombre":"MARIPOSA GRANDE","numart":"        MARIPOSA GRD"},
  {"existencia_total":"5814.000","nombre":"MINI-VÁLV. INICIAL C/GOMA X CINTA 5/8\" AZUL","numart":"             MV0017B"}
]}
```

POST /api/v3/bi_inventario/1/stock_actual?limit=5&offset=0&numalm=2
```json
{"result":[
  {"existencia_total":"1245.000","nombre":"BLOCK NO 4 CURADO","numart":"                0060"},
  {"existencia_total":"1140.000","nombre":"BISAGRA CUADRADA 2-1/2', LATONADO, CABEZA PLANA, HERMEX","numart":"       7501206671085"},
  {"existencia_total":"1026.000","nombre":"ENVASE 1 LITRO","numart":"             0000001"},
  {"existencia_total":"1003.000","nombre":"BOLSA CON 1 KG DE CLAVOS PARA CONCRETO NEGROS 3/4', FIERO","numart":"       7501206693742"},
  {"existencia_total":"993.894","nombre":"KILO DE CUERDA AMARILLA DE POLIPROPILENO 11MM, CARRETE 20KG","numart":"       7506240608042"}
]}
```

POST /api/v3/bi_inventario/1/stock_actual?limit=5&numalm=02 (código válido sin existencias)
```json
{"result":null}
```

```sh
curl -X POST -H "X-sait-api-key: $APIKEY" \
  "https://test.saitnube.com/api/v3/bi_inventario/1/stock_actual?limit=10&offset=0&numalm=2"
```

---

### Valor por familia

POST /api/v3/bi_inventario/:pk/valor_familia?limit=..&offset=..&numalm=..

Regresa la existencia total agrupada por familia de artículo (`bi_inventario_existencias.familia`, copiada de `arts.familia` en la carga), tomando el nombre de familia desde el catálogo `familias`, ordenado de mayor a menor existencia y, en empates, por código de familia (paginación determinista). Con `numalm` primero se toman solo las existencias de esa sucursal y después se agrupa por familia.

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |
| numalm | (opcional) Almacén/sucursal. Ver [Filtro numalm](#filtro-numalm) |

Campos
| Campo            | Tipo   | Significado                                                                                                                                      |
|------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| familia          | string | Número de identificación de la familia (`arts.familia`). `""` si viene vacía o `NULL`                                                           |
| nombre           | string | Nombre de la familia (`familias.nomfam`); `"Sin familia"` si `familia` viene vacía, `"Familia sin nombre"` si el nombre del catálogo está vacío. `""` si la familia es `NULL` (artículo que no existe en `arts`) o no existe en el catálogo |
| existencia_total | string | Suma de la existencia de los artículos de esa familia en los almacenes consultados (decimal como texto)                                         |

Response real (foto cargada desde la base local)

POST /api/v3/bi_inventario/1/valor_familia?limit=5&offset=0
```json
{"result":[
  {"existencia_total":"50908.048","familia":"","nombre":"Sin familia"},
  {"existencia_total":"47017.200","familia":"PLOME","nombre":"PLOMERÍA Y CONSTRUCCIÓN"},
  {"existencia_total":"33044.083","familia":"ASPER","nombre":"ASPERSORES"},
  {"existencia_total":"21039.586","familia":" P549","nombre":"Tornillos, tuercas y arandelas"},
  {"existencia_total":"17805.925","familia":"JARDI","nombre":"EQUIPO DE JARDINERIA"}
]}
```

POST /api/v3/bi_inventario/1/valor_familia?limit=5&offset=0&numalm=2
```json
{"result":[
  {"existencia_total":"7662.242","familia":"","nombre":"Sin familia"},
  {"existencia_total":"2660.416","familia":"PLOME","nombre":"PLOMERÍA Y CONSTRUCCIÓN"},
  {"existencia_total":"2637.352","familia":" P727","nombre":"Cables conductores"},
  {"existencia_total":"2077.000","familia":" P013","nombre":"Bisagras"},
  {"existencia_total":"1731.185","familia":" P513","nombre":"Pijas"}
]}
```

```sh
curl -X POST -H "X-sait-api-key: $APIKEY" \
  "https://test.saitnube.com/api/v3/bi_inventario/1/valor_familia?limit=10&offset=0&numalm=2"
```

#### Errores (stock_actual y valor_familia)

| HTTP | Cuándo |
|------|--------|
| 400  | `numalm` de más de 2 caracteres o que no sea texto |
| 500  | Falla la consulta (por ejemplo, no existe `bi_inventario_existencias`). La respuesta solo dice `bi_inventario.stockActual: error al consultar la base de datos` (o `valorPorFamilia`); el detalle se escribe en el log del servidor |

#### Pruebas

`bi/inventario/stock_familia_test.go` (integración con `SAITNUBE_TEST_DSN`, igual que [Compras del periodo](#compras-del-periodo)):

```sh
SAITNUBE_TEST_DSN='usuario:password@tcp(127.0.0.1:3306)/' go test ./bi/inventario/ -run 'Stock|Numalm' -v
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
- **Redondeo**: el total en MXN de cada documento se redondea a centavos (`ROUND(..., 2)` de MySQL).
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
| 400  | `fini`/`ffin` faltantes o inválidas, `fini > ffin`, `groupby` no válido                                  |
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

---

### Existencias fuera de limites

POST /api/v3/bi_inventario/:pk/reposicion?limit=10&offset=0&estado=agotado&numalm=..&numart=..&q=..&incluir_sin_limites=false

Datos de la tarjeta "Existencias fuera de límites": combinaciones **artículo + sucursal** cuya **existencia física** está agotada, bajo el mínimo o arriba del máximo configurados. Es inventario **actual**: no recibe fechas, no usa ventas ni pronósticos y no calcula disponible, cantidades por comprar ni traspasos. No requiere body. `:pk` es parte de la ruta y no filtra.

Para ver un artículo en todas las sucursales, el frontend usa `GET /api/v3/existencias/:numart` (existencia, mínimo y máximo; ver `inventario/multialm/readme.md`).

#### Origen de los campos

Cada fila es un registro de `multialm`, que tiene llave primaria `(numart, numalm)`, por lo que hay una sola fila por combinación. `arts` y `almacen` se unen por su llave primaria, así que no multiplican filas.

| Campo      | Origen                                                                                          |
|------------|-------------------------------------------------------------------------------------------------|
| numart     | `multialm.numart` sin los espacios de alineación (`"   0075"` → `"0075"`)                       |
| nombre     | `arts.desc`; `"Articulo sin nombre"` si está vacío o el artículo no existe en `arts`            |
| numalm     | `multialm.numalm` sin los espacios de alineación (`" 2"` → `"2"`, `"02"` → `"02"`)              |
| sucursal   | `almacen.nomalm` sin espacios; si el almacén no existe en `almacen` o el nombre es `NULL`/vacío: `"Almacén {numalm}"` (ej. `"Almacén 4"`, `"Almacén 04"`). Es el mismo valor que `nomalm` en `GET /existencias/:numart` |
| existencia | `multialm.existencia` (existencia física)                                                       |
| minimo     | `multialm.minimo`                                                                               |
| maximo     | `multialm.maximo`                                                                               |

Los valores se regresan como están en la base de datos: un `NULL` se regresa como `null` y **no se convierte en 0**. `GET /existencias/:numart` regresa los mismos valores de `existencia`, `minimo` y `maximo`.

**No se usan `apartados` ni `ordenados`.** No están sincronizados: la sincronización con SAIT (`utils/eventos/syncsd.go`) solo escribe los campos que trae cada evento, y los eventos `MULTIALM` documentados (`doc/evento.md`, `utils/eventos/readme.md`) solo traen `existencia`, `costopro`, `ventanoqty`, `numart` y `numalm`. En la base local ambos son `NULL` en todas las filas aunque hay pedidos y órdenes de compra pendientes, así que `NULL` ahí significa "no sincronizado", no cero.

#### Clasificación

Límites `NULL` o `0` = **no configurados**. No se inventa el límite que falta: con solo mínimo positivo se puede evaluar `bajo_minimo`; con solo máximo positivo, `sobreinventario`.

**Límites inválidos**: mínimo negativo, máximo negativo, o ambos positivos con `maximo < minimo`.

Si `existencia` es `NULL` o los límites son inválidos, la combinación **no se clasifica** (aunque la existencia sea 0), no aparece en `filas` y se cuenta en `datos_incompletos`.

Las demás se clasifican con la existencia física, en este orden (la primera que se cumple):

| estado          | Condición                                                   |
|-----------------|-------------------------------------------------------------|
| agotado         | `existencia <= 0` (incluye negativos)                        |
| bajo_minimo     | `existencia > 0`, mínimo positivo y `existencia < minimo`    |
| sobreinventario | máximo positivo y `existencia > maximo`                      |
| (no se lista)   | En rango: exactamente en el mínimo o en el máximo, entre ambos, o sin el límite que aplicaría |

Las comparaciones se hacen en SQL con `DECIMAL`, sin pasar por `float` (`3.749` con mínimo `3.75` es `bajo_minimo`).

#### Filtros

| Filtro | Descripcion                                                                                                            |
|--------|------------------------------------------------------------------------------------------------------------------------|
| limit  | (opcional) Entero de 1 a 100. Default 10                                                                                |
| offset | (opcional) Entero >= 0, cantidad de **registros** a saltar (no número de página). Default 0                              |
| estado | (opcional) `agotado`, `bajo_minimo` o `sobreinventario`, en minúsculas. Omitido o vacío: los tres                        |
| numalm | (opcional) Almacén exacto, texto de máximo 2 caracteres. Se alinea a la derecha con espacios como en SAIT: `2` es `" 2"`, `02` es otro almacén |
| numart | (opcional) Artículo exacto, texto de máximo 20 caracteres, alineado a la derecha: `0075` y `75` son artículos distintos |
| q      | (opcional) Texto de máximo 100 caracteres. Se quitan espacios al inicio y al final; vacío equivale a no enviarlo         |
| incluir_sin_limites | (opcional) `true` o `false`. **Default `false`**: solo entran al análisis combinaciones con `minimo > 0` o `maximo > 0`. Con `true` también entran las demás. Cualquier otro valor (incluido vacío) responde 400 |

`q` sigue la convención de `GET /compras`: se separa en palabras y **cada palabra** debe aparecer (coincidencia parcial) en el código o nombre del artículo, o en el código o nombre de la sucursal (el nombre que se muestra, incluido `"Almacén {numalm}"`). No distingue mayúsculas/minúsculas (collation de la base de datos). Los caracteres `%`, `_` y `!` se buscan como texto (`q=100%` no es un comodín) y el valor va parametrizado.

`incluir_sin_limites` solo decide qué combinaciones entran al análisis; no vuelve válidos los límites inválidos ni cambia la clasificación. Con `true`, una combinación sin ningún límite positivo solo puede ser `agotado` (`existencia <= 0`); nunca `bajo_minimo` ni `sobreinventario`. La falta de límites por sí sola **no** cuenta en `datos_incompletos` (sí la existencia `NULL` o un límite negativo). En la base local, con `true` hay 21,683 agotados contra 3 del modo por defecto: por eso el default es `false`.

#### Conteos y paginación

| Campo             | Cálculo                                                                                                   |
|-------------------|-----------------------------------------------------------------------------------------------------------|
| resumen.*         | Combinaciones por estado con `q`, `numalm`, `numart` e `incluir_sin_limites`, **sin aplicar `estado`**: los conteos de la tarjeta no cambian al seleccionar uno. `resumen.total = agotados + bajo_minimo + sobreinventario` |
| total             | Combinaciones con todos los filtros, incluido `estado`, antes de `limit`/`offset`                           |
| datos_incompletos | Combinaciones con `q`, `numalm`, `numart` e `incluir_sin_limites` que no se pudieron clasificar (existencia `NULL` o límites inválidos). Sin `estado`; no se suman a `total` ni a `resumen.total` |
| hay_mas           | `offset + filas regresadas < total`                                                                        |

`limit` y `offset` solo afectan `filas`. Todos los conteos son de combinaciones artículo/sucursal, no de artículos distintos.

Orden fijo: `agotado`, `bajo_minimo`, `sobreinventario`; dentro de cada estado por `numart` y `numalm` ascendentes. Se ordena por las claves como están guardadas en SAIT (alineadas a la derecha), por lo que los códigos más cortos van primero (`75` antes de `0075`, `WTH1` antes de `7506240611172`). Como `(numart, numalm)` es único, el orden es determinista y no repite filas entre páginas mientras los datos no cambien.

Clasificación, filtro, orden y `LIMIT/OFFSET` se hacen en SQL: una consulta para los conteos y otra para la página. `arts` y `almacen` solo se unen a las filas de la página, salvo cuando `q` necesita buscar en sus nombres.

Sin resultados, o con `offset >= total`: `filas: []`, `hay_mas: false` y `total` con su valor real. `resumen` puede tener conteos aunque el `estado` seleccionado no tenga filas.

#### Errores

| HTTP | Cuándo                                                                                                    |
|------|-----------------------------------------------------------------------------------------------------------|
| 400  | `limit`/`offset` no enteros o fuera de rango, `estado` desconocido, `incluir_sin_limites` distinto de `true`/`false`, `q` de más de 100 caracteres, `numalm` de más de 2 o `numart` de más de 20 caracteres |
| 500  | Falla la consulta. La respuesta solo dice `bi_inventario.reposicion: error al consultar la base de datos`; el detalle se escribe en el log del servidor |

```json
{ "error": "bi_inventario.reposicion: incluir_sin_limites debe ser true o false" }
```

#### Respuesta

HTTP 201 (igual que las demás acciones `POST` de este módulo).

| Campo                  | Tipo           | Significado                                          |
|------------------------|----------------|------------------------------------------------------|
| limit / offset         | int            | Paginación aplicada                                  |
| total                  | int            | Total con todos los filtros, para paginar            |
| hay_mas                | bool           | Hay más filas después de esta página                  |
| resumen                | objeto         | Conteos por estado sin el filtro `estado`            |
| datos_incompletos      | int            | Combinaciones que no se pudieron clasificar          |
| filas                  | array          | Siempre arreglo, `[]` si no hay filas                 |
| filas[].numart         | string         | Artículo, conserva ceros a la izquierda              |
| filas[].nombre         | string         | Nombre del artículo                                  |
| filas[].numalm         | string         | Almacén/sucursal, conserva ceros a la izquierda      |
| filas[].sucursal       | string         | Nombre del almacén                                   |
| filas[].existencia     | number         | Existencia física, nunca `null` en `filas`            |
| filas[].minimo         | number o null  | Mínimo                                               |
| filas[].maximo         | number o null  | Máximo                                               |
| filas[].estado         | string         | `agotado`, `bajo_minimo` o `sobreinventario`          |

Respuesta real con la base local:

POST /api/v3/bi_inventario/1/reposicion?limit=10&offset=0
```json
{"result":{"limit":10,"offset":0,"total":23,"hay_mas":true,
 "resumen":{"total":23,"agotados":3,"bajo_minimo":5,"sobreinventario":15},
 "datos_incompletos":20,
 "filas":[
  {"numart":"700795083447","nombre":"PINTURA BERELINTE AZUL REY 834  LITRO","numalm":"2","sucursal":"Almacen 2","existencia":0,"minimo":1,"maximo":6,"estado":"agotado"},
  {"numart":"700795160032","nombre":"BARNIZ SUMMA TRANSPARENTE 1600    500 ML","numalm":"2","sucursal":"Almacen 2","existencia":0,"minimo":1,"maximo":3,"estado":"agotado"},
  {"numart":"700795262354","nombre":"IMPERMEABILIZANTE KOVER 5A CRYL BCO 4 LT","numalm":"2","sucursal":"Almacen 2","existencia":0,"minimo":3,"maximo":6,"estado":"agotado"},
  {"numart":"WTH1","nombre":"ANTICONGELANTE DILUIDO 30% ROSA GALO","numalm":"1","sucursal":"Almacen 1","existencia":2,"minimo":240,"maximo":500,"estado":"bajo_minimo"},
  {"numart":"7506240611172","nombre":"MANGUERA P/WC, ACERO INOX, 1/2'X7/8', 35 CM, PLÁSTICO/LATÓN","numalm":"6","sucursal":"Almacén 6","existencia":3,"minimo":40,"maximo":0,"estado":"bajo_minimo"},
  {"numart":"7506610214897","nombre":"PIOLA DE POLIPROPILENO TRENZADA, MULTICOLOR, 6MM, 1 KG","numalm":"8","sucursal":"LA FINCA","existencia":6,"minimo":189,"maximo":195,"estado":"bajo_minimo"},
  {"numart":"BIBEL 1950120","nombre":"BIBEL MEDIANO 19X50X120","numalm":"1","sucursal":"Almacen 1","existencia":1,"minimo":4,"maximo":4,"estado":"bajo_minimo"},
  {"numart":"27506240669764","nombre":"46196 MALLA MOSQUITERA GAL. 0.9 X 11 M","numalm":"6","sucursal":"Almacén 6","existencia":1,"minimo":5,"maximo":0,"estado":"bajo_minimo"},
  {"numart":"700795006934","nombre":"PINTURA SUMMA NEGRO 0624 500ML","numalm":"2","sucursal":"Almacen 2","existencia":24,"minimo":1,"maximo":3,"estado":"sobreinventario"},
  {"numart":"700795062336","nombre":"PINTURA SUMMA BLANCO BASE PASTEL 623  500ML","numalm":"2","sucursal":"Almacen 2","existencia":22,"minimo":1,"maximo":6,"estado":"sobreinventario"}
 ]}}
```

Las 55 combinaciones locales con algún límite positivo se reparten en 23 fuera de límites, 20 con `existencia` `NULL` (`datos_incompletos`) y 12 en rango.

```sh
# agotados, primera pagina
curl -X POST -H "X-sait-api-key: $APIKEY" \
  "https://test.saitnube.com/api/v3/bi_inventario/1/reposicion?limit=10&offset=0&estado=agotado"

# sobreinventario de la sucursal 02
curl -X POST -H "X-sait-api-key: $APIKEY" \
  "https://test.saitnube.com/api/v3/bi_inventario/1/reposicion?estado=sobreinventario&numalm=02"

# incluir combinaciones sin minimo ni maximo positivos
curl -X POST -H "X-sait-api-key: $APIKEY" \
  "https://test.saitnube.com/api/v3/bi_inventario/1/reposicion?incluir_sin_limites=true&estado=agotado"

# busqueda por texto, segunda pagina
curl -X POST -H "X-sait-api-key: $APIKEY" \
  "https://test.saitnube.com/api/v3/bi_inventario/1/reposicion?q=pintura%20summa&limit=10&offset=10"
```

#### Pruebas

`bi/inventario/reposicion_test.go` (integración con `SAITNUBE_TEST_DSN`, igual que [Compras del periodo](#compras-del-periodo)):

```sh
SAITNUBE_TEST_DSN='usuario:password@tcp(127.0.0.1:3306)/' go test ./bi/inventario/ -run 'Reposicion|Cantidad|Like' -v
```
