## dashboard

### Parametros

| parametro | descripcion                                                                           |
|-----------|---------------------------------------------------------------------------------------|
| metrica   | (1: Ventas Brutas, 2: Devoluciones, 3: Ventas Netas, 4: Costo de Ventas, 5: Ganancia) |
| dia       | Obtener ventas del día especificado (será necesario especificar mes y año)            |
| semana    | Obtener ventas de la semana especificada (será necesario especificar mes y año)       |
| mes       | Obtener ventas del mes especificado (será necesario especificar año)                  |
| trimestre | Obtener ventas del trimestre especificado (será necesario especificar año)            |
| semestre  | Obtener ventas del semestre especificado (será necesario especificar año)             |
| ano       | Obtener ventas del año especificado (será necesario especificar año)                  |
| fini      | Fecha inicial a considerar (20250101)                                                 |
| ffin      | Fecha limite a considerar  (20251231)                                                 |
| groupby   | Agrupar por: dia, semana, mes, trimestre, semestre, ano                               |
| limit     | limitar cantidad de registros                                                         |

### Respuesta

| parametro | descripcion                                |
|-----------|--------------------------------------------|
| clave     | clave unica para identificar cada registro |
| nombre    | Nombre para identificar cada registro      |
| valor     | valor de cada registro                     |

---
### Ventas Totales

GET api/v3/bi/ventas/totales

Ejemplos:
```
GET api/v3/bi/ventas/totales?ano=2023&trimestre=1&groupby=mes

{
    "ventas_brutas": 15384948.61,
    "devoluciones": 15104.05,
    "ventas_netas": 15284590.2,
    "costos": 4440818.76,
    "ganancias": 10847843.38
}
```

---
### Ventas x fecha de la empresa

GET api/v3/bi/ventas/empresa

Ejemplos:
```
GET api/v3/bi/ventas/empresa?metrica=1&ano=2023&trimestre=1&groupby=mes 

[
    {
        "clave" : "20231"
        "nombre" : "1",
        "valor" : 51120.15
    },
    {
        "clave" : "20232"
        "nombre" : "2",
        "valor" : 51120.15
    },
    {
        "clave" : "20233"
        "nombre" : "3",
        "valor" : 51120.15
    }
]
```

```
GET api/v3/bi/ventas/empresa?metrica=1&ano=2023&groupby=semestre

[
    {
        "clave" : "20231"
        "nombre" : "1",
        "valor" : 51120.15
    },
    {
        "clave" : "20232",
        "nombre" : "2",
        "valor" : 51120.15
    }
]
```

---
### Ventas Top Sucursales

GET api/v3/bi/ventas/sucursales

Ejemplo:
```
GET api/v3/bi/ventas/sucursales?metrica=1&ano=2023&trimestre=1

[
    {
        "clave" : "1",
        "nombre" : "Matriz 1",
        "valor" : 51120.15
    },
    {
        "clave" : "2",
        "nombre" : "Bimbo",
        "valor" : 51120.15
    },
]
```

---
### Ventas Top Clientes

GET api/v3/bi/ventas/clientes

Ejemplo:
```
GET api/v3/bi/ventas/clientes?metrica=1&ano=2023&trimestre=1

[
    {
        "clave" : "MSL1",
        "nombre" : "Microsistemas San Luis",
        "valor" : 51120.15
    },
    {
        "clave" : "BIM2",
        "nombre" : "Bimbo",
        "valor" : 51120.15
    },
]
```

---
### Ventas Top Articulos

GET api/v3/bi/ventas/articulos

Ejemplo:
```
GET api/v3/bi/ventas/articulos?metrica=1&ano=2023&semestre=1

[
    {
        "clave" : "COCA1",
        "nombre" : "Coca cola",
        "valor" : 51120.15
    },
    {
        "clave" : "BI2",
        "nombre" : "Pan Bimbo",
        "valor" : 51120.15
    },
]
```

---
### Ventas Top Lineas

GET api/v3/bi/ventas/lineas

Ejemplo:
```
GET api/v3/bi/ventas/lineas?metrica=1&ano=2023&semana=1

[
    {
        "clave" : "1",
        "nombre" : "Bebidas",
        "valor" : 51120.15
    },
    {
        "clave" : "2",
        "nombre" : "Comida",
        "valor" : 51120.15
    },
]
```

---
### Ventas Top Categorias

GET api/v3/bi/ventas/categorias

Ejemplo:
```
GET api/v3/bi/ventas/categorias?metrica=1&ano=2023&semana=1

[
    {
        "clave" : "1",
        "nombre" : "catego1",
        "valor" : 51120.15
    },
    {
        "clave" : "2",
        "nombre" : "catego2",
        "valor" : 51120.15
    },
]
```

---
### Ventas Top Familias

GET api/v3/bi/ventas/familias

Ejemplo:
```
GET api/v3/bi/ventas/familias?metrica=1&ano=2023&semana=1

[
    {
        "clave" : "1",
        "nombre" : "fam1",
        "valor" : 51120.15
    },
    {
        "clave" : "2",
        "nombre" : "fam2",
        "valor" : 51120.15
    },
]
```