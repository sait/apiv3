## Calcular precios

Rutas de Clientes

| Accion                                | Ruta                                   |
|---------------------------------------|----------------------------------------|
| [calcular precios](#calcular-precios) | GET /api/v3/calcularprecios?filtros... |

---
### calcular precios

GET /api/v3/calcularprecios?filtros

| Variables entrada | Significado                                                         |
|-------------------|---------------------------------------------------------------------|
| numart            | numero de articulo     (obligatorio)                                |
| unidad            | unidad de articulo     (obligatorio)                                |
| cant              | cantidad de articulos  (obligatorio)                                |
| numcli            | numero de cliente      (obligatorio)                                |
| numalm            | numero de almacen      (obligatorio)                                |
| formapago         | 0: ambos, 1: contado, 2: Credito  (obligatorio)                     |
| divisadoc         | divisa que utilizara el documento (obligatorio)                     |
| numclisuc         |                                                                     |
| pjedescfact       |                                                                     |
| preciolista       | selecciona uno de los 5 precios disponibles del articulo(1,2,3,4,5) |

| Ejemplo de Búsqueda           | Ruta                                                                                             |
|-------------------------------|--------------------------------------------------------------------------------------------------|
| calcular precio de art: GLAPE | /api/v3/calcularprecios?numart=GLAPE&numcli=5&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1 |


response:
```json
{
    "precio"     : 36.1111,
    "preciopub"  : 39,
    "pjedesc"    : 0,
    "opcionapli" : 1
}
```