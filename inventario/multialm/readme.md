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
| nomalm      | string   | Nombre de almacen. Si el almacén no existe en `almacen` o su nombre está vacío: `"Almacén {numalm}"` (ej. `"Almacén 4"`), igual que `sucursal` en `bi_inventario/:pk/reposicion` (Existencias fuera de límites) |
| existencia  | float o null | Existencia del artículo en el almacén          |
| maximo      | float o null | Máxima existencia del artículo                 |
| minimo      | float o null | Minimo existencia del artículo                 |
| reorden     | float    | Mínimo para reorden                                |
| ubica       | string   | ubicacion del articulo                             |
| ventanoqty  | float    | Venta por año                                      |
| apartados   | float    | Cantidad apartada en pedidos (0 si es NULL)        |
| ordenados   | float    | Cantidad en ordenes de compra (0 si es NULL)       |
| costopro    | float    | Costo Promedio                                     |

`existencia`, `maximo` y `minimo` regresan `null` cuando la columna es `NULL` en `multialm` (antes regresaban `0`). `NULL` significa que la sincronización con SAIT nunca envió ese campo (solo se escriben los campos que trae cada evento), no que valga 0.

`apartados` y `ordenados` conservan su contrato original: regresan `0` cuando la columna es `NULL`, porque otras integraciones los reciben así. Hoy no están sincronizados (son `NULL` en todas las filas de la base local), por lo que ese `0` no es confiable; la tarjeta "Existencias fuera de límites" no los usa.

---

### Buscar existencias

GET /api/v3/existencias/numart?filtros

| Variable | Significado                                             |
|----------|---------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0    |
| limit    | Cuantos registros obtener. Default 100                  |
| order    | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc |
| numalm   | Numero de almacen                                       |

response real (base local), GET /api/v3/existencias/700795083447:
```json
{"result":[
  {"numart":"        700795083447","numalm":" 1","nomalm":"Almacen 1","existencia":2,"maximo":null,"minimo":null,"reorden":0,"ubica":"","ventanoqty":4,"apartados":0,"ordenados":0,"costopro":78.78,"ultcosto":85.65},
  {"numart":"        700795083447","numalm":" 2","nomalm":"Almacen 2","existencia":0,"maximo":6,"minimo":1,"reorden":0,"ubica":"","ventanoqty":3,"apartados":0,"ordenados":0,"costopro":83.57146,"ultcosto":85.65},
  {"numart":"        700795083447","numalm":" 4","nomalm":"Almacén 4","existencia":0,"maximo":null,"minimo":null,"reorden":0,"ubica":"","ventanoqty":0,"apartados":0,"ordenados":0,"costopro":0,"ultcosto":85.65}
],"error":""}
```

ejemplo anterior:
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