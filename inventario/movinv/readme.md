## Kardex de articulos

| Accion            | Ruta                                          |
|-------------------|-----------------------------------------------|
| [Kardex](#kardex) | GET  /api/v3/movinv/:numart/kardex?filters... |

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

GET  /api/v3/movinv/:numart/kardex?filters

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

GET  /api/v3/movinv/:numart/kardex?totalizar=true

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