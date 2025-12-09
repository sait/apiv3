## Calcular precios

Ruta para calcular el mejor precio de un articulo

| Accion                                | Ruta                                   |
|---------------------------------------|----------------------------------------|
| [calcular precios](#calcular-precios) | GET /api/v3/calcularprecios?filtros... |

| filtros     | Significado                                                         |
|-------------|---------------------------------------------------------------------|
| numart      | numero de articulo     (obligatorio)                                |
| unidad      | unidad de articulo     (obligatorio)                                |
| cant        | cantidad de articulos  (obligatorio)                                |
| numcli      | numero de cliente      (obligatorio)                                |
| numalm      | numero de almacen      (obligatorio)                                |
| formapago   | 0: ambos, 1: contado, 2: Credito  (obligatorio)                     |
| divisadoc   | divisa que utilizara el documento (obligatorio)                     |
| pjedescfact |                                                                     |
| preciolista | selecciona uno de los 5 precios disponibles del articulo(1,2,3,4,5) |

ejemplo: /api/v3/calcularprecios?numart=0164&numcli=0&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

[ver ejemplo de las 9 promociones](./ejemplos/readme.md)


response:
```json
{
    "precio": 25,
    "preciopub": 27,
    "pjedesc": 0,
    "opcionapli": 1,
    "preciolista": "1"
}
```