## Kardex de articulos

| Accion                                            | Ruta                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|
| [Kardex](#kardex)                                 | POST   /api/v3/movinv/:numart/kardex?filters...                 |
| [Existencias en almacén](#existencias-en-almacén) | POST   /api/v3/movinv/:numalm/existencias_en_almacen?filtros... |

Campos kardex

| Campo      | Tipo   | Significado                                        |
|------------|--------|----------------------------------------------------|
| id         | int    | id incremental disponible solo através de SAIT API |
| fecha      | string | fecha de creacion de registro                      |
| numalm     | string | numero de identificacion de almacen                |
| tipodoc    | string | Tipo de documento relacionado a registro           |
| numdoc     | string | Numero de documento relacionado                    |
| numpar     | string | Numero de partida relacionada                      |
| entradas   | string | cantidad de entradas en movimiento                 |
| disp       | string |                                                    |
| salidas    | string | cantidad de salidas en movimiento                  |
| costo      | string | costo por unidad de articulo                       |
| costo_mov  | string | costo x cantidad de articulo                       |
| existencia | string | sumatoria de cantidad en registros                 |
| valor_inv  | string | sumatoria de costos en registros                   |

tabla de costos

| Costo | Descripcion       |
|-------|-------------------|
| 1     | Segun capas (MXN) |
| 2     | Promedio (MXN)    |
| 3     | Ultimo (MXN)      |
| 4     | Segun capas (EXT) |

---
### Kardex

POST /api/v3/movinv/:numart/kardex?filters

filtros

| Filtro  | Descripcion                                                                                        |
|---------|----------------------------------------------------------------------------------------------------|
| fecha1  | A partir de tal fecha para tomar registros                                                         |
| fecha2  | Hasta tal fecha para tomar registros                                                               |
| numalm  | Numero de identificacion del almacen                                                               |
| costo   | 1: Segun capas (MXN), 2: Promedio (MXN), 3: Ultimo (MXN), 4: Segun capas (EXT - Moneda extrangera) |
| mostrar | e: Solo mostrar Entradas, s: Solo mostrar Salidas, c: solo mostrar con campo disp > 0              |
| limit   | cantidad de registros a tomar                                                                      |
| offset  | tomar registros a partir del registro X                                                            |

Response
```json
[
  {
    "costo": "9.8",
    "costo_mov": "9.8",
    "disp": "0.000",
    "entradas": "0.000",
    "existencias": "-97.000",
    "fecha": "2020-01-04",
    "numalm": " 1",
    "numdoc": "    A41512",
    "numpar": "  2",
    "salidas": "1.000",
    "tipodoc": " N",
    "valor_inv": "-950.6"
  },
  {}...
]
```

Totalizar resultado:

POST /api/v3/movinv/:numart/kardex?totalizar=true

response:
```json
{
    "disp": "4.000",
    "entradas": "5.000",
    "existencias": "-1.000",
    "items": "5",
    "salidas": "2.000",
    "valor_inv": "0"
}
```



---

## Existencias en almacén

```
POST /api/v3/movinv/:numalm/existencias_en_almacen?filtros
```

Campos existencias en almacén

| Campo              | Tipo   | Significado                                                      |
|--------------------|--------|------------------------------------------------------------------|
| numart             | string | numero de identificacion del articulo                            |
| desc               | string | descripcion del articulo                                         |
| unidad             | string | unidad de medida del articulo                                    |
| existencia_inicial | string | existencia acumulada antes de fecha1                             |
| entradas           | string | sumatoria de entradas entre fecha1 y fecha2                      |
| salidas            | string | sumatoria de salidas entre fecha1 y fecha2                       |
| existencia_final   | string | existencia acumulada total (entradas - salidas) dentro del rango |


filtros

| Filtro    | Descripcion                                                                                                        |
|-----------|--------------------------------------------------------------------------------------------------------------------|
| fecha1    | (obligatorio) A partir de tal fecha para tomar registros                                                           |
| fecha2    | (obligatorio) Hasta tal fecha para tomar registros                                                                 |
| numalm    | (obligatorio) Numero de identificacion del almacen                                                                 |
| q         | Busqueda de palabras dentro de la descripcion del articulo (hasta 4 palabras separadas por espacio)                |
| familia   | Numero de identificacion de familia del articulo                                                                   |
| linea     | Numero de identificacion de linea del articulo                                                                     |
| categoria | Numero de identificacion de categoria del articulo                                                                 |
| marca     | Numero de identificacion de marca del articulo                                                                     |
| order     | Campo para ordenar resultado: desc, numart, existencia, linea, categoria. Agregar "-" antes para orden descendente |
| limit     | cantidad de registros a tomar                                                                                      |
| offset    | tomar registros a partir del registro X                                                                            |

Response
```json
[
  {
    "desc":"FRANELA DE ALGODON",
    "entradas":"0.000",
    "existencia_final":"-7.000",
    "existencia_inicial":"0.000",
    "numart":"                   0",
    "salidas":"7.000",
    "unidad":"PIEZA"
  },
  {}...
]
```

Totalizar resultado:

```
POST /api/v3/movinv/:numalm/existencias_en_almacen?filters...&totalizar=true
```

response:
```json
{
  "items":"18778"
}
```