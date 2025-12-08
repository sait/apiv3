## Existencias - Multialm

Rutas de existencias

| Accion                        | Ruta                                          |
|-------------------------------|-----------------------------------------------|
| [Buscar](#buscar-existencias) | GET    /api/v3/existencias/:numalm?filtros... |

Campos de los existencias

| Campo       | Tipo     | Significado                                        |
|-------------|----------|----------------------------------------------------|
| id          | int      | id incremental disponible solo através de SAIT API |
| created     | datetime | timestamp del momento de creación                  |
| updated     | datetime | timestamp ultima actualizacion                     |
| numart      | string   | Clave de articulo                                  |
| numalm      | string   | Numero de almacen                                  |
| nomalm      | string   | Nombre de almacen                                  |
| existencias | float    | Existencia del artículo en el almacén              |
| maximo      | float    | Máxima existencia del artículo                     |
| minimo      | float    | Minimo existencia del artículo                     |
| reorden     | float    | Mínimo para reorden                                |
| ubica       | string   | ubicacion del articulo                             |
| ventanoqty  | float    | Venta por año                                      |
| apartados   | float    | Cantidad apartada en pedidos                       |
| ordenados   | float    | Cantidad en ordenes de compra                      |
| costopro    | float    | Costo Promedio                                     |

---

### Buscar existencias

GET /api/v3/existencias/numart?filtros

| Variable | Significado                                             |
|----------|---------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0    |
| limit    | Cuantos registros obtener. Default 100                  |
| order    | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc |
| numalm   | Numero de almacen                                       |

response:
```json
[
    {
      "numart": "                 SCC",
      "numalm": " 1",
      "nomalm": "MATRIZ",
      "existencia": 740,
      "maximo": 0,
      "minimo": 0,
      "reorden": 0,
      "ubica": "",
      "ventanoqty": 677,
      "apartados": 0,
      "ordenados": 0,
      "costopro": 35.39910125732422
    },
    {
      "numart": "                 SCC",
      "numalm": " 2",
      "nomalm": "KINO",
      "existencia": -16,
      "maximo": 0,
      "minimo": 0,
      "reorden": 0,
      "ubica": "",
      "ventanoqty": 0,
      "apartados": 0,
      "ordenados": 0,
      "costopro": 0
    },
    {}...
]
```