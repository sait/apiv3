## Bi Inventario

| Accion                                                                | Ruta                                                               |
|-----------------------------------------------------------------------|--------------------------------------------------------------------|
| [Top artículos vendidos](#top-articulos-vendidos)                     | POST   /api/v3/bi_inventario/:pk/top_articulos?fecha1=..&fecha2=.. |
| [Valor de inventario por sucursal](#valor-de-inventario-por-sucursal) | POST   /api/v3/bi_inventario/:pk/valor_sucursal                    |

> `:pk` (obligatorio) actualmente no se usa para filtrar la consulta, pero es requerido por la ruta. Ejemplo: `1`.

---

### Top artículos vendidos

POST /api/v3/bi_inventario/:pk/top_articulos?fecha1=..&fecha2=..

Regresa los 10 artículos con mayor cantidad vendida, considerando como venta los movimientos de tipo Factura (`" F"`) y Nota de venta (`" N"`) en la tabla `minv`.

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
| cant_vend | float  | Suma de las unidades vendidas (`cant`, en valor absoluto) del artículo en el rango dado |

Response
```json
{
  "result": [
    { "numart": "  A102", "cant_vend": "845.0000" },
    { "numart": "  B045", "cant_vend": "712.0000" },
    { "numart": "  C019", "cant_vend": "530.0000" },
    {}
  ]
}
```

---

### Valor de inventario por sucursal

POST /api/v3/bi_inventario/:pk/valor_sucursal

Regresa el valor actual de inventario (cantidad x costo) agrupado por almacén/sucursal, a partir de la tabla `minv`.

Campos
| Campo            | Tipo   | Significado                                                      |
|------------------|--------|------------------------------------------------------------------|
| numalm           | string | Número de identificación del almacén/sucursal                    |
| valor_inventario | float  | Suma de `cantidad x costo` de todos los artículos en ese almacén |

Response
```json
{
  "result": [
    { "numalm": " 01", "valor_inventario": "1250430.0000" },
    { "numalm": " 02", "valor_inventario": "685920.5000" },
    {}
  ]
}
```