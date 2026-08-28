## Bi Inventario

| Accion                                                                | Ruta                                                               |
|-----------------------------------------------------------------------|--------------------------------------------------------------------|
| [Stock actual](#stock-actual)                                         | POST   /api/v3/bi_inventario/:pk/stock_actual                      |
| [Valor por familia](#valor-por-familia)                               | POST   /api/v3/bi_inventario/:pk/valor_familia                     |
| [Top artículos vendidos](#top-articulos-vendidos)                     | POST   /api/v3/bi_inventario/:pk/top_articulos?fecha1=..&fecha2=.. |
| [Valor de inventario por sucursal](#valor-de-inventario-por-sucursal) | POST   /api/v3/bi_inventario/:pk/valor_sucursal                    |

> `:pk` (obligatorio) actualmente no se usa para filtrar la consulta, pero es requerido por la ruta. Ejemplo: `1`.

> Pendientes de implementar: `totales`, `indice_rotacion`, `margenes`, `unidades_vendidas`.

---

### Stock actual

POST /api/v3/bi_inventario/:pk/stock_actual

Regresa el top 20 de artículos con mayor existencia total, sumando la existencia de todos los almacenes/sucursales (tabla `multialm`), junto con su nombre/descripción (tabla `arts`).

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

POST /api/v3/bi_inventario/:pk/valor_familia

Regresa la existencia total agrupada por familia de artículo (`arts.numfam`), tomando el nombre de familia desde el catálogo `familias` (join por `arts.linea`).

Campos
| Campo            | Tipo   | Significado                                                                                                                                     |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| numfam           | string | Número de identificación de la familia (`arts.numfam`)                                                                                          |
| nombre           | string | Nombre de la familia (`familias.nomfam`); `"Sin familia"` si `numfam` viene vacío, `"Familia sin nombre"` si no hay coincidencia en el catálogo |
| existencia_total | float  | Suma de la existencia de todos los artículos de esa familia (todos los almacenes)                                                               |

Response
```json
{
  "result": [
    { "numfam": " 01", "nombre": "FERRETERIA", "existencia_total": "15420.0000" },
    { "numfam": " 02", "nombre": "PLOMERIA", "existencia_total": "9830.0000" },
    { "numfam": "", "nombre": "Sin familia", "existencia_total": "312.0000" },
    {}
  ]
}
```

---

### Top artículos vendidos

POST /api/v3/bi_inventario/:pk/top_articulos?fecha1=..&fecha2=..

Regresa los 10 artículos con mayor cantidad vendida, considerando como venta los movimientos de tipo Factura (`" F"`) y Nota de venta (`" N"`) en la tabla `minv`, junto con su nombre/descripción (tabla `arts`).

filtros
| Filtro | Descripcion                                                                    |
|--------|--------------------------------------------------------------------------------|
| fecha1 | (opcional) Fecha inicial del rango a consultar — filtra `minv.fecha >= fecha1` |
| fecha2 | (opcional) Fecha final del rango a consultar — filtra `minv.fecha <= fecha2`   |

> Si no se envían `fecha1` ni `fecha2`, el cálculo considera todos los movimientos históricos de `minv` (sin acotar por fecha).

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
    { "numart": "  C019", "nombre": "CINTA TEFLON", "cant_vend": "530.0000" },
    {}
  ]
}
```

---

### Valor de inventario por sucursal

POST /api/v3/bi_inventario/:pk/valor_sucursal

Regresa el valor de inventario (cantidad x costo) agrupado por almacén/sucursal, a partir de la tabla `minv`, junto con el nombre del almacén (tabla `almacen`).

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