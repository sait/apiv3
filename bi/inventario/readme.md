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
| [Aumentos de precio en compras](#aumentos-de-precio-en-compras)       | GET    /api/v3/bi_inventario/:pk/aumentos_precio?fini=..&ffin=..&numalm=..&numprov=..    |

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

POST /api/v3/bi_inventario/:pk/totales?numalm=..&fecha=..

Regresa el resumen general del inventario: cantidad de artículos distintos, cantidad de almacenes distintos y el valor total de inventario (existencia x costo) en toda la empresa o en una sucursal, a partir de `bi_inventario_existencias` (foto de `multialm` de la última carga de las tablas bi).

filtros
| Filtro | Descripcion |
|--------|-------------|
| numalm | (opcional) Almacén/sucursal, igual que en [Stock actual](#stock-actual) |
| fecha  | (opcional) `YYYY-MM-DD`. Totales con la existencia al cierre de ese día. Sin `fecha`, vacía o con la fecha de hoy: existencia actual |

Campos
| Campo                  | Tipo  | Significado                                                                   |
|------------------------|-------|-------------------------------------------------------------------------------|
| total_numart           | int   | Cantidad de artículos distintos (`numart`) con registro en la foto (aunque su existencia sea 0) |
| total_numalm           | int   | Cantidad de almacenes distintos (`numalm`) con registro en la foto            |
| valor_total_inventario | float | Suma de `existencia x costopro` de los artículos en los almacenes consultados |

#### Existencia al cierre de una fecha

Con `fecha` los campos son los mismos, pero la existencia de cada artículo y almacén es la del cierre de ese día:

```
existencia_al_corte = existencia de la foto − cant_mov de bi_inventario_mov con id_fecha > fecha
```

- `cant_mov` es la suma de `minv.cant` con signo (entradas +, salidas −). Se reconstruye hacia atrás desde la foto porque `minv` no siempre tiene el historial completo (inventarios iniciales), pero sí los movimientos posteriores.
- Se valúa a **costo promedio actual** (`bi_inventario_existencias.costopro`), no al costo que tenía el artículo en esa fecha.
- `total_numart` y `total_numalm` cuentan lo mismo que sin fecha: los registros de la foto, incluidos artículos dados de alta después de la fecha. Un artículo/almacén que ya no está en `multialm` no se cuenta.
- `bi_inventario_mov` cubre todo el historial de `minv`; los movimientos con `fecha` NULL no se cargan, así que nunca se restan (cuentan como anteriores a cualquier fecha).
- La foto y `bi_inventario_mov` deben ser de la misma carga ([dashboard_inventario.sql](../../../database/dbtest/dashboard_inventario.sql)).
- "Hoy" es la fecha en la zona horaria `America/Mexico_City` (UTC-6), sin importar la zona del servidor.

Errores (400)
| Caso | error |
|------|-------|
| Formato inválido (`20260115`, `2026-02-30`) | `bi_inventario.totales: fecha debe ser una fecha valida en formato YYYY-MM-DD` |
| Fecha futura | `bi_inventario.totales: fecha 2026-12-01 es futura, debe ser menor o igual a hoy (2026-11-24)` |
| Antes del primer movimiento cargado | `bi_inventario.totales: fecha 2023-12-31 es anterior al primer movimiento cargado (2024-01-25)` |
| `bi_inventario_mov` vacía | `bi_inventario.totales: no hay movimientos cargados en bi_inventario_mov para calcular la existencia a una fecha` |
| La empresa no tiene la columna `cant_mov` | `bi_inventario.totales: el parametro fecha estara disponible despues de la proxima actualizacion y recarga de las tablas bi de inventario` |

Ejemplo: inventario de la sucursal 1 al cierre de 2025

POST /api/v3/bi_inventario/1/totales?fecha=2025-12-31&numalm=1

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

POST /api/v3/bi_inventario/:pk/stock_actual?limit=..&offset=..&numalm=..&familia=..&linea=..&categoria=..

Regresa la existencia total por artículo de `bi_inventario_existencias` (todos los almacenes o solo `numalm`), junto con su nombre/descripción (tabla `arts`), ordenado de mayor a menor existencia y, en empates, por `numart`.

Los filtros `numalm`, `familia`, `linea` y `categoria` se aplican antes de sumar, ordenar y paginar, y se pueden combinar entre sí. Sin ellos el resultado es el de toda la empresa.

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |
| numalm | (opcional) Almacén/sucursal, se alinea a 2 caracteres (`2` = `" 2"`, `02` es otro almacén) |
| familia | (opcional) Clave de familia del artículo (`arts.familia`, catálogo `familias`). Máximo 5 caracteres, se alinea a 5 |
| linea | (opcional) Clave de línea del artículo (`arts.linea`, catálogo `lineas`). Máximo 5 caracteres, se alinea a 5 |
| categoria | (opcional) Clave de categoría del artículo (`arts.categoria`, catálogo `catego`). Máximo 5 caracteres, se alinea a 5 |
| depto | (opcional) Número de departamento de **nivel 1** (`deptos.numdep`, por ejemplo `1` o `50`). Se alinea a la derecha en 3 caracteres como `valdep` (`1` = `"  1"`). No incluye subdepartamentos |

La clasificación es la copiada de `arts` en la última carga de `bi_inventario_existencias`. Un filtro vacío (`familia=`) no filtra; una clave de más de 5 caracteres regresa 400:

```json
{"error": "bi_inventario.stockActual: familia debe tener maximo 5 caracteres"}
```

**`depto` solo soporta departamentos de nivel 1.** En SAIT el `valdep` de nivel 1 es `numdep` alineado a la derecha en 3 caracteres (`"1"` → `"  1"`, `"50"` → `" 50"`, ver [deptos](../../inventario/deptos/readme.md)). `valdep` mide 9 caracteres y `numdep` 11, lo que sugiere subdepartamentos de hasta 3 niveles, pero en el código y los datos disponibles no hay ningún ejemplo de cómo arma SAIT el `valdep` de un subdepartamento ni qué separador usa `numdep`. Para no inventar esa regla, un `depto` de más de 3 caracteres o con separador (`.`, `-`, espacio…) regresa 400:

```json
{"error": "bi_inventario.stockActual: depto: subdepartamentos aún no soportados, envie el numero de departamento de nivel 1 (maximo 3 caracteres, sin separadores)"}
```

`depto` usa la columna `valdep`, que se agregó a `bi_inventario_existencias`. Si la empresa todavía no tiene las tablas bi actualizadas responde 400:

```json
{"error": "bi_inventario.stockActual: el filtro depto estara disponible despues de la proxima actualizacion y recarga de las tablas bi de inventario"}
```

Ejemplo: artículos de la línea `L1` en la sucursal 2

POST /api/v3/bi_inventario/1/stock_actual?linea=L1&numalm=2&limit=10

Campos
| Campo            | Tipo   | Significado                                                                             |
|------------------|--------|-----------------------------------------------------------------------------------------|
| numart           | string | Número de identificación del artículo                                                   |
| nombre           | string | Descripción / nombre del artículo (`arts.desc`); `"Articulo sin nombre"` si viene vacío |
| existencia_total | string | Suma de la existencia del artículo en los almacenes consultados                         |

Response
```json
{"result":[
  {"existencia_total":"24246.000","nombre":"MICRO SPRAY 360°, 110LPH","numart":"               SJ360"},
  {"existencia_total":"15895.000","nombre":"PIJAS HEXAGONAL, PUNTA DE BROCA 14-3\"","numart":"               81211"}
]}
```

---

### Valor por familia

POST /api/v3/bi_inventario/:pk/valor_familia?limit=..&offset=..&numalm=..&agrupar=..

Regresa la existencia total agrupada por familia de artículo (`bi_inventario_existencias.familia`), tomando el nombre de familia desde el catálogo `familias`, ordenado de mayor a menor existencia y, en empates, por código de familia. Con `numalm` solo se suman las existencias de esa sucursal.

Con `agrupar` se agrupa por otra clasificación del artículo con la misma lógica. Sin `agrupar` (o con `agrupar=familia`) la respuesta es la de siempre.

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
| limit  | (opcional) Cantidad de registros a tomar           |
| offset | (opcional) Tomar registros a partir del registro X |
| numalm | (opcional) Almacén/sucursal, igual que en [Stock actual](#stock-actual) |
| agrupar | (opcional) `familia` (default), `linea`, `categoria` o `depto`. Otro valor regresa 400 |

| agrupar   | Campo de la clave | Catálogo (clave, nombre)  | Sin clasificación | Nombre vacío en el catálogo |
|-----------|-------------------|---------------------------|-------------------|-----------------------------|
| familia   | `familia`         | `familias` (numfam, nomfam) | `"Sin familia"`   | `"Familia sin nombre"`      |
| linea     | `linea`           | `lineas` (numlin, nomlin)   | `"Sin línea"`     | `"Línea sin nombre"`        |
| categoria | `categoria`       | `catego` (numcat, nomcat)   | `"Sin categoría"` | `"Categoría sin nombre"`    |
| depto     | `depto` (es el `valdep`) | `deptos` (valdep, nomdep) | `"Sin departamento"` | `"Departamento sin nombre"` |

En todas las agrupaciones, incluida `familia`:
- Si la clave del artículo **no existe en el catálogo**, `nombre` es `""` (cadena vacía), no `null` ni `"... sin nombre"`.
- Si el artículo no existe en `arts` (su clasificación quedó NULL en la carga), se agrupa aparte con la clave `""` y `nombre` `""`. Es un grupo distinto del de `"Sin familia"` (clave vacía en `arts`), aunque los dos muestran la clave `""`.

`agrupar=depto` agrupa por departamento exacto (`valdep`, tal como viene de `arts`): si una empresa tuviera subdepartamentos, cada uno sería otro grupo (no se suman a su departamento de nivel 1). Si la empresa todavía no tiene la columna `valdep` en `bi_inventario_existencias` responde 400 (`agrupar=depto estara disponible despues de la proxima actualizacion y recarga de las tablas bi de inventario`).

```json
{"error": "bi_inventario.valorPorFamilia: agrupar 'marca' no valido, opciones: categoria, depto, familia, linea"}
```

Campos
| Campo            | Tipo   | Significado                                                                                                                                      |
|------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| familia          | string | Número de identificación de la familia (con `agrupar`, el campo se llama `linea`, `categoria` o `depto`)                                         |
| nombre           | string | Nombre de la familia (`familias.nomfam`); `"Sin familia"` si `familia` viene vacía, `"Familia sin nombre"` si el nombre del catálogo está vacío, `""` si la clave no existe en el catálogo |
| existencia_total | string | Suma de la existencia de los artículos de esa familia en los almacenes consultados                                                               |

Response
```json
{"result":[
  {"existencia_total":"50908.048","familia":"","nombre":"Sin familia"},
  {"existencia_total":"47017.200","familia":"PLOME","nombre":"PLOMERÍA Y CONSTRUCCIÓN"}
]}
```

POST /api/v3/bi_inventario/1/valor_familia?agrupar=linea&numalm=1&limit=10
```json
{"result":[
  {"existencia_total":"8120.000","linea":"","nombre":"Sin línea"},
  {"existencia_total":"5310.500","linea":"   L1","nombre":"LINEA UNO"}
]}
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

POST /api/v3/bi_inventario/:pk/alertas?fini=20260401&ffin=20260430&tipo=..&dias_cobertura=..&limit=..&offset=..

Cruza la existencia **actual** de cada artículo (suma de todos los almacenes en `bi_inventario_existencias`, igual que [Stock actual](#stock-actual)) con sus unidades vendidas en el [rango](#rango-fini-ffin) (`cant_vend` de `bi_inventario_mov`, mismo criterio que [Total de unidades vendidas](#total-de-unidades-vendidas)). Existencias y ventas se agrupan por artículo antes de cruzarse, para no multiplicar filas. Solo considera artículos con registro en `bi_inventario_existencias`. Si un artículo no tuvo ventas en el rango, sus ventas cuentan como 0.

`dias_periodo` son los días calendario del rango, incluyendo ambos extremos.

Cada artículo queda como máximo en una alerta, evaluadas en este orden:

| tipo           | Condición                                                                                             |
|----------------|-------------------------------------------------------------------------------------------------------|
| stock_negativo | `existencia_total < 0`, sin importar las ventas                                                        |
| sin_ventas     | `existencia_total > 0` y `unidades_vendidas = 0`                                                       |
| cobertura_baja | `existencia_total >= 0`, `unidades_vendidas > 0` y cobertura **estrictamente menor a `dias_cobertura`** (default 14 días) |

Cobertura = `existencia_total × dias_periodo ÷ unidades_vendidas`. La comparación contra `dias_cobertura` se hace con el valor sin redondear (con el default, una cobertura de exactamente 14 días no genera alerta). Con existencia 0 y ventas positivas, la cobertura es 0.

filtros
| Filtro    | Descripcion                                                                                           |
|-----------|-------------------------------------------------------------------------------------------------------|
| fini/ffin | (obligatorio) [Rango de fechas](#rango-fini-ffin)                                                     |
| tipo      | (opcional) `stock_negativo`, `sin_ventas` o `cobertura_baja`. Sin `tipo` regresa solo el resumen        |
| dias_cobertura | (opcional) Entero mayor a 0, días debajo de los cuales hay `cobertura_baja`. Default 14. Aplica al resumen y al detalle; vacío = default |
| limit     | (opcional) Entero mayor a 0, cantidad de artículos del detalle. Default 10                             |
| offset    | (opcional) Entero mayor o igual a 0, tomar artículos a partir del registro X. Default 0                |

Un `dias_cobertura` que no sea entero mayor a 0 (`0`, `-3`, `1.5`, `abc`) regresa 400:

```json
{"error": "bi_inventario.alertas: dias_cobertura debe ser un entero mayor a 0"}
```

Ejemplo: cobertura baja con umbral de 30 días

POST /api/v3/bi_inventario/1/alertas?fini=20260401&ffin=20260430&tipo=cobertura_baja&dias_cobertura=30

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

---

### Existencias fuera de limites

POST /api/v3/bi_inventario/:pk/reposicion?limit=10&offset=0&estado=agotado&numalm=..&numart=..&q=..&incluir_sin_limites=false

Datos de la tarjeta "Existencias fuera de límites": combinaciones **artículo + sucursal** de `multialm` cuya existencia está agotada, bajo el mínimo o arriba del máximo. Es inventario actual, no recibe fechas. Los `NULL` de existencia, mínimo y máximo se toman como `0`.

| estado          | Condición                                    |
|-----------------|----------------------------------------------|
| agotado         | `existencia <= 0`                            |
| bajo_minimo     | mínimo > 0 y `existencia < minimo`           |
| sobreinventario | máximo > 0 y `existencia > maximo`           |

filtros
| Filtro | Descripcion                                                                                     |
|--------|-------------------------------------------------------------------------------------------------|
| limit  | (opcional) 1 a 100. Default 10                                                                  |
| offset | (opcional) Registros a saltar. Default 0                                                        |
| estado | (opcional) `agotado`, `bajo_minimo` o `sobreinventario`. Omitido: los tres                       |
| numalm | (opcional) Almacén exacto, se alinea a 2 caracteres                                             |
| numart | (opcional) Artículo exacto, se alinea a 20 caracteres                                           |
| q      | (opcional) Cada palabra debe aparecer en el código o nombre del artículo o de la sucursal       |
| incluir_sin_limites | (opcional) `true` incluye combinaciones sin mínimo ni máximo positivos. Default `false` |

- `resumen` son los conteos por estado **sin** aplicar `estado`, para que la tarjeta no cambie al seleccionar uno.
- `total` es el conteo con todos los filtros, para paginar; `hay_mas = offset + filas < total`.
- Orden: `agotado`, `bajo_minimo`, `sobreinventario` y dentro de cada uno por `numart`, `numalm`.
- `sucursal` es `almacen.nomalm` o `"Almacén {numalm}"` si no existe o está vacío, igual que `nomalm` en `GET /existencias/:numart`.

Response (HTTP 201)
```json
{"result":{"limit":10,"offset":0,"total":23,"hay_mas":true,
 "resumen":{"total":23,"agotados":3,"bajo_minimo":5,"sobreinventario":15},
 "filas":[
  {"numart":"700795083447","nombre":"PINTURA BERELINTE AZUL REY 834  LITRO","numalm":"2","sucursal":"Almacen 2","existencia":0,"minimo":1,"maximo":6,"estado":"agotado"},
  {"numart":"WTH1","nombre":"ANTICONGELANTE DILUIDO 30% ROSA GALO","numalm":"1","sucursal":"Almacen 1","existencia":2,"minimo":240,"maximo":500,"estado":"bajo_minimo"}
 ]}}
```

#### Pruebas

`bi/inventario/*_test.go`. Las pruebas de integración crean una base de datos temporal y requieren `SAITNUBE_TEST_DSN`:

```sh
cd src
SAITNUBE_TEST_DSN='usuario:password@tcp(127.0.0.1:3306)/' go test ./bi/inventario/ -v
```

---

### Aumentos de precio en compras

GET /api/v3/bi_inventario/:pk/aumentos_precio?fini=20260201&ffin=20260228&numalm=..&numprov=..

También responde como acción `POST /api/v3/bi_inventario/:pk/aumentos_precio` (HTTP 201) con los mismos parámetros.

Por cada combinación **artículo + proveedor + sucursal + unidad** regresa la última compra válida hasta `ffin` y la compra inmediatamente anterior de esa misma combinación. Regresa aumentos, disminuciones y precios sin cambio; el frontend (`calcularAumentosPrecio`) calcula y filtra la variación.

| Filtro  | Descripcion                                                                                   |
|---------|-----------------------------------------------------------------------------------------------|
| fini    | (obligatorio) [Rango](#rango-fini-ffin) `YYYYMMDD`. Solo se aplica a la fecha de la compra actual |
| ffin    | (obligatorio) [Rango](#rango-fini-ffin) `YYYYMMDD`. Se ignoran las compras posteriores        |
| numalm  | (opcional) Sucursal de la compra (`docum.numalm`), se alinea a 2 caracteres. Vacío = todas    |
| numprov | (opcional) Proveedor de la compra (`docum.numprov`), se alinea a 5 caracteres. Vacío = todos  |

#### Origen

```
docum d
  INNER JOIN movim m     ON m.tipodoc = d.tipodoc AND m.numdoc = d.numdoc
  INNER JOIN arts a      ON a.numart = m.numart
  INNER JOIN provedor p  ON p.numprov = d.numprov      -- proveedor de la compra, no arts.numprov
  INNER JOIN almacen al  ON al.numalm = d.numalm
```

- Compras válidas: `TRIM(d.tipodoc) = 'C'`, `status` distinto de `1` (`NULL` es vigente), `d.fecha <= ffin`, `m.cant > 0`. Quedan fuera órdenes (`' O'`), devoluciones (`'DC'`), ventas y canceladas.
- Un proveedor, sucursal o artículo que no existe en su catálogo descarta **todas** las compras de esa combinación; nunca deja huecos en el historial.
- Partidas del mismo artículo y unidad en un documento se consolidan: `cantidad = SUM(cant)` y costo `SUM(cant * costo) / SUM(cant)`. Si una partida no tiene costo, el consolidado es `null`.
- Historial por `(numart, numprov, numalm, unidad)` ordenado por `fecha DESC, hora DESC, tipodoc DESC, numdoc DESC`. La primera es `compra_actual` y la siguiente es `compra_anterior`, aunque sea anterior a `fini`. Si `compra_actual` es anterior a `fini`, la combinación no se regresa.
- `unidad` es `movim.unidad` sin espacios (`""` si viene vacía o `NULL`); no se reemplaza por `arts.unidad`.

#### Costo unitario (`costo_unitario_mxn`)

Precio de adquisición de la partida, neto de descuentos, sin IVA ni IEPS, en MXN, redondeado a 6 decimales:

```
movim.precio * (1 - movim.pjedesc / 100) * factor_descuentog * tipo_cambio
factor_descuentog = 1 - docum.descuentog / (docum.importe - docum.descuento)   (1 si descuentog es 0)
tipo_cambio       = 1 si divisa = 'P'; docum.tc si divisa = 'D' y tc > 0; si no, null
```

- `movim.pjedesc` es el descuento efectivo de la partida: `docum.descuento` es la suma de `cant * precio * pjedesc / 100`, por eso no se vuelve a aplicar.
- No se aplican `docum.pjedesc` (se copia a las partidas o no afecta importes), `docum.descuento1..4` (desglose de `descuento`) ni `movim.pjedesc1..4` (no intervienen en los importes de compras).
- `descuentog` es un descuento global aparte (GET /compras lo resta del total además de `descuento`), se prorratea sobre el subtotal neto.
- Una compra en dólares sin tipo de cambio válido, con otra divisa o sin precio regresa `costo_unitario_mxn: null`. No se usa el tipo de cambio actual ni `costo`, `costo2` o `costopro`.

#### Respuesta

| Campo                         | Origen                                                     |
|-------------------------------|------------------------------------------------------------|
| id                            | `precio-{numart}\|{numprov}\|{numalm}\|{unidad}`, estable por combinación |
| numart / articulo             | `movim.numart` / `arts.desc`                               |
| numprov / proveedor           | `docum.numprov` / `provedor.nomprov`                       |
| numalm / sucursal             | `docum.numalm` / `almacen.nomalm`                          |
| unidad                        | `movim.unidad`                                             |
| compra_*.fecha                | `docum.fecha` (`YYYY-MM-DD`)                               |
| compra_*.folio                | `{tipodoc}-{numdoc}` sin espacios, ej. `C-E3613`           |
| compra_*.cantidad             | Cantidad comprada de la combinación en el documento        |
| compra_*.costo_unitario_mxn   | Costo unitario neto en MXN o `null`                        |

Las claves se regresan como texto sin alinear. `compra_anterior` es `null` si no hay compra previa. Sin resultados regresa `{"result":[]}`.

Response (HTTP 200)
```json
{"result":[
 {"id":"precio-420|E132|1|","numart":"420","articulo":"TORNILLO CILINDRICO MILIMETRICO 4MM X 20MM",
  "numprov":"E132","proveedor":"WURTH","numalm":"1","sucursal":"Almacen 1","unidad":"",
  "compra_anterior":{"fecha":"2025-06-10","folio":"C-E2764","cantidad":50,"costo_unitario_mxn":1.54},
  "compra_actual":{"fecha":"2026-06-16","folio":"C-E3613","cantidad":200,"costo_unitario_mxn":1.26}},
 {"id":"precio-0|E134|1|","numart":"0","articulo":"FRANELA DE ALGODON",
  "numprov":"E134","proveedor":"FREDIY TRAPO","numalm":"1","sucursal":"Almacen 1","unidad":"",
  "compra_anterior":null,
  "compra_actual":{"fecha":"2025-07-18","folio":"C-E2850","cantidad":24,"costo_unitario_mxn":21.55}}
]}
```

Errores
| HTTP | Causa                                                                                        |
|------|----------------------------------------------------------------------------------------------|
| 400  | `fini`/`ffin` faltantes o inválidas, `fini > ffin`, `numalm` de más de 2 o `numprov` de más de 5 caracteres |
| 500  | Error de base de datos: `{"error":"bi_inventario.aumentosPrecio: error al consultar la base de datos"}`; el detalle queda en el log |

Pruebas: `go test ./bi/inventario/ -run 'Aumentos|ClaveOpt|IdComparativo' -v` (ver [Pruebas](#pruebas)).
