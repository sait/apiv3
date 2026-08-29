## Bi Inventario

| Accion                                                                | Ruta                                                                                      |
|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| [Totales](#totales)                                                   | POST   /api/v3/bi_inventario/:pk/totales                                                  |
| [Stock actual](#stock-actual)                                         | POST   /api/v3/bi_inventario/:pk/stock_actual?limit=..&offset=..                          |
| [Valor por familia](#valor-por-familia)                               | POST   /api/v3/bi_inventario/:pk/valor_familia?limit=..&offset=..                         |
| [Top artículos vendidos](#top-articulos-vendidos)                     | POST   /api/v3/bi_inventario/:pk/top_articulos?fecha1=..&fecha2=..&limit=..&offset=..     |
| [Valor de inventario por sucursal](#valor-de-inventario-por-sucursal) | POST   /api/v3/bi_inventario/:pk/valor_sucursal?limit=..&offset=..                        |
| [Índice de rotación](#indice-de-rotacion)                             | POST   /api/v3/bi_inventario/:pk/indice_rotacion?periodo=mes\|anio                        |
| [Márgenes de ganancia](#margenes-de-ganancia)                         | POST   /api/v3/bi_inventario/:pk/margenes?fecha1=..&fecha2=..&limit=..&offset=..          |
| [Unidades vendidas por artículo](#unidades-vendidas-por-articulo)     | POST   /api/v3/bi_inventario/:pk/unidades_vendidas?fecha1=..&fecha2=..&limit=..&offset=.. |

> `:pk` (obligatorio) actualmente no se usa para filtrar la consulta, pero es requerido por la ruta. Ejemplo: `1`.

---

### Conceptos generales

Varias rutas de este módulo consideran como "venta" únicamente los movimientos de la tabla `minv` que cumplen **ambas** condiciones:
- `tipodoc = ' F'` (Factura) o `tipodoc = ' N'` (Nota de venta)
- `cant` negativo (salida de almacén), verificado con `abs(cant) != cant`

En ninguna de las rutas `limit`/`offset` tienen un valor por defecto: si no se envían, la consulta regresa todos los registros sin límite.

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

POST /api/v3/bi_inventario/:pk/top_articulos?fecha1=..&fecha2=..&limit=..&offset=..

Regresa los artículos con mayor cantidad vendida (ver [criterio de venta](#conceptos-generales)), junto con su nombre/descripción (tabla `arts`), ordenado de mayor a menor cantidad vendida.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| fecha1 | (opcional) Fecha inicial del rango a consultar — filtra `minv.fecha >= fecha1` |
| fecha2 | (opcional) Fecha final del rango a consultar — filtra `minv.fecha <= fecha2`   |
| limit  | (opcional) Cantidad de registros a tomar                                       |
| offset | (opcional) Tomar registros a partir del registro X                             |

> Si no se envían `fecha1` ni `fecha2`, el cálculo considera todos los movimientos históricos de `minv`.

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

POST /api/v3/bi_inventario/:pk/valor_sucursal?limit=..&offset=..

Regresa el valor de inventario (cantidad x costo) agrupado por almacén/sucursal, a partir de la tabla `minv`, junto con el nombre del almacén (tabla `almacen`).

filtros
| Filtro | Descripcion                                        |
|--------|----------------------------------------------------|
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

POST /api/v3/bi_inventario/:pk/indice_rotacion?periodo=mes

Calcula el índice de rotación de inventario y los días que tardaría en venderse todo el inventario actual, dentro del **mes actual** o del **año actual** (según servidor).

`indice_rotacion` = costo de ventas del periodo (ver [criterio de venta](#conceptos-generales)) / valor actual del inventario.
`dias_rotacion` = días del periodo entre `indice_rotacion` — usando **365** cuando `periodo="anio"` o **30** cuando `periodo="mes"` (calculado en Go antes de armar la consulta, no en SQL).

filtros
| Filtro  | Descripcion                                                                                |
|---------|--------------------------------------------------------------------------------------------|
| periodo | (obligatorio) Rango a calcular: `mes` (mes actual completo) o `anio` (año actual completo) |

> El rango de fechas se calcula directamente en la base de datos con `CURDATE()`: para `mes` toma del día 1 al último día del mes en curso (`LAST_DAY`); para `anio` toma del 1 de enero al 31 de diciembre del año en curso.

> El inventario se toma como el valor **actual** de existencias (`multialm`), no un promedio histórico entre inicio y fin del periodo, ya que no se lleva snapshot histórico de existencias.

Campos
| Campo           | Tipo  | Significado                                                                                                                                                |
|-----------------|-------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| indice_rotacion | float | Costo de ventas del periodo entre el valor actual del inventario                                                                                           |
| dias_rotacion   | float | Días que tardaría en venderse todo el inventario actual al ritmo de venta del periodo (`365/indice_rotacion` para `anio`, `30/indice_rotacion` para `mes`) |

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

POST /api/v3/bi_inventario/:pk/margenes?fecha1=..&fecha2=..&limit=..&offset=..

Regresa ventas totales, costo total y ganancia por artículo (ver [criterio de venta](#conceptos-generales)), ordenado de mayor a menor ganancia.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| fecha1 | (opcional) Fecha inicial del rango a consultar — filtra `minv.fecha >= fecha1` |
| fecha2 | (opcional) Fecha final del rango a consultar — filtra `minv.fecha <= fecha2`   |
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

POST /api/v3/bi_inventario/:pk/unidades_vendidas?fecha1=..&fecha2=..&limit=..&offset=..

Regresa las unidades vendidas agrupadas por artículo (ver [criterio de venta](#conceptos-generales)), ordenado de mayor a menor cantidad vendida.

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| fecha1 | (opcional) Fecha inicial del rango a consultar — filtra `minv.fecha >= fecha1` |
| fecha2 | (opcional) Fecha final del rango a consultar — filtra `minv.fecha <= fecha2`   |
| limit  | (opcional) Cantidad de registros a tomar                                       |
| offset | (opcional) Tomar registros a partir del registro X                             |

Campos
| Campo            | Tipo   | Significado                                                            |
|------------------|--------|------------------------------------------------------------------------|
| numart           | string | Número de identificación del artículo                                  |
| cantidad_vendida | float  | Suma de las unidades vendidas (`cant`, en valor absoluto) del artículo |

Response
```json
{
  "result": [
    { "numart": "  A102", "cantidad_vendida": "845.0000" },
    { "numart": "  B045", "cantidad_vendida": "712.0000" },
    {}
  ]
}
```