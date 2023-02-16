# Articulos

Rutas de Artículos

| Acción         | Ruta                                  |
| -------------- | ------------------------------------- |
| Crear          | POST /api/v3/articulos                 |
| Actualizar     | PUT  /api/v3/articulos/:id             |
| Leer por id    | GET  /api/v3/articulos/:id             |
| Leer por clave | GET  /api/v3/articulos/clave/:key      |
| Buscar         | GET  /api/v3/articulos?condiciones...  |


Campos de Artículo

| Campo      | Significado                                            |
| ---------- | ------------------------------------------------------ |
| id         | id incremental disponible solo através de SAIT API     |
| created    | timestamp del momento de creación                      |
| updated    | timestamp ultima actualizacion                         |
| numart     | clave del articulo                                     |
| desc       | descripción del articulo                               |
| obs        | descripción larga u observaciones del artículo         |
| linea      | clave de linea para clasificacion                      |
| familia    | clave de familia para clasificacion                    |
| categoria  | clave de categoria para clasifiacion                   |
| activo     | boleano para indicar si articulo está activo           |
| marca      |                                                        |
| model      |                                                        |
| unidad     | unidad de venta: PZ,CJ,MTS                             |
| precio1    | precio segun lista 1 sin impuestos                     |
| precio2    | precio segun lista 2 sin impuestos                     |
| precio3    | precio segun lista 3 sin impuestos                     |
| precio4    | precio segun lista 4 sin impuestos                     |
| precio5    | precio segun lista 5 sin impuestos                     |
| preciopub  | precio publico con impuestos incluidos                 |
| divisa     | moneda para precio P=Pesos D=Dolar                     |
| ultcosto   | ultimo costo de compra                                 |
| clavesat   | clave segun SAT                                        |
| excento    | boleando indicando si es excento de impuestos para SAT |
| impuesto1  | % de IVA segun SAT                                     |
| impuesto2  | % de IEPS segun SAT                                    |
| esmatpelig | Es Material Peligroso segun SAT                        |
| matpelig   | Clave SAT para Material Peligroso                      |
| existencia | Existencia TOTAL sumando TODAS las sucursales          |


### Crear Artículo

POST /api/v3/articulos

request:
```json
{
    "numart": "           GRC-55657",
    "desc": "AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146",
    "activo": 1,
    "categoria": "  SUS",
    "clavesat": "25172000",
    "codigo": "55657",
    "divisa": "P",
    "esmatpelig": 0,
    "excento": 0,
    "existencia": 0,
    "familia": "SUS02",
    "impuesto1": 16,
    "impuesto2": 0,
    "linea": "  114",
    "marca": "GRC",
    "matpelig": "",
    "modelo": "",
    "obs": "",
    "precio1": 689.59,
    "precio2": 500.87,
    "precio3": 466.19,
    "precio4": 441.71,
    "precio5": 355,
    "preciopub": 799.92,
    "ultcosto": 0,
    "unidad": "PZA  "
 ```

response:
```json
{
    "id": 1,
    "created": "2023-02-05 21:53:21",
    "updated": "2023-02-06 15:23:57",
    "numart": "           GRC-55657",
    "desc": "AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146",
    "activo": 1,
    "categoria": "  SUS",
    "clavesat": "25172000",
    "codigo": "55657",
    "divisa": "P",
    "esmatpelig": 0,
    "excento": 0,
    "existencia": 0,
    "familia": "SUS02",
    "impuesto1": 16,
    "impuesto2": 0,
    "linea": "  114",
    "marca": "GRC",
    "matpelig": "",
    "modelo": "",
    "obs": "",
    "precio1": 689.59,
    "precio2": 500.87,
    "precio3": 466.19,
    "precio4": 441.71,
    "precio5": 355,
    "preciopub": 799.92,
    "ultcosto": 0,
    "unidad": "PZA  "
 ```


### Actualizar Artículo

PUT /api/v3/articulos/:id

request:
```json
{
    "id": 1,
    "desc": "AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146"
}
```

response:
```json
"UPDATED"
```

### Leer Artículo por Id

GET /api/v3/articulos/:id


### Leer Artículo por Clave

GET /api/v3/articulos/clave/:clave


### Buscar Artículos

GET /api/v3/articulos?variables

| Variable | Significado                                          |
| -------- | ---------------------------------------------------- |
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,numart         |
| q        | Palabras a buscar                                    |


| Ejemplo de Búsqueda          | Ruta                                |
| ---------------------------- | ----------------------------------- |
| Buscar los primeros 25 arts  | /api/v3/articulos?limit=25          |
| Buscar rótulas               | /api/v3/articulos?q=rotula          |
| Buscar rótulas para Suburban | /api/v3/articulos?q=rotula+suburban |
| Leer artículo por clave      | /api/v3/articulos/clave/MOO-K6145   |
| Leer artículo por id         | /api/v3/articulos/3929              |

