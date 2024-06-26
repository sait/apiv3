## Artículos

Representan a los objetos que son comprados y vendidos por la empresa. **Los servicios que presta la empresa también se incluyen en este catálogo**, marcando el campo boleano *arts.servicio* como verdadero.

### Rutas de Artículos

| Acción                                                 | Ruta                               |
| ------------------------------------------------------ | ---------------------------------- |
| [Crear](#crear-artículo)                               | POST /api/v3/articulos             |
| [Actualizar](#actualizar-artículo)                     | PUT  /api/v3/articulos/:numart     |
| [Leer](#leer-artículo)                                 | GET  /api/v3/articulos/:numart     |
| [Leer por UPC](#leer-artículo-por-upc)                 | GET  /api/v3/articulos/upc/:upc    |
| [Leer por UPC o Clave](#leer-artículo-por-upc-o-clave) | GET  /api/v3/articulos/upccve/:txt |
| [Buscar](#buscar-artículos)                            | GET  /api/v3/articulos?condiciones |


### Campos del Artículo

| Campo      | Significado                                               |
| ---------- | --------------------------------------------------------- |
| created    | timestamp del momento de creación                         |
| updated    | timestamp ultima actualizacion                            |
| numart     | clave del articulo (número de artículo) |
| desc       | descripción del articulo                                  |
| codigo     | upc o código de barra del articulo                        |
| obs        | descripción larga u observaciones del artículo            |
| linea      | clave de linea para clasificacion                         |
| familia    | clave de familia para clasificacion                       |
| categoria  | clave de categoria para clasifiacion                      |
| activo     | boleano para indicar si articulo está activo              |
| marca      |                                                           |
| model      |                                                           |
| unidad     | unidad de venta: PZ,CJ,MTS                                |
| precio1    | precio segun lista 1 sin impuestos                        |
| precio2    | precio segun lista 2 sin impuestos                        |
| precio3    | precio segun lista 3 sin impuestos                        |
| precio4    | precio segun lista 4 sin impuestos                        |
| precio5    | precio segun lista 5 sin impuestos                        |
| preciopub  | precio publico con impuestos incluidos                    |
| divisa     | moneda para precio P=Pesos D=Dolar                        |
| ultcosto   | ultimo costo de compra                                    |
| clavesat   | clave segun SAT                                           |
| excento    | boleando indicando si es excento de impuestos para SAT    |
| impuesto1  | % de IVA segun SAT                                        |
| impuesto2  | % de IEPS segun SAT                                       |
| esmatpelig | Es Material Peligroso segun SAT                           |
| matpelig   | Clave SAT para Material Peligroso                         |
| existencia | Existencia TOTAL sumando TODAS las sucursales             |



---
### Crear Artículo

POST /api/v3/articulos
##### Request  
```json
{
    "numart": "GRC-55657",
    "desc": "AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146",
    "activo": 1,
    "categoria": "SUS",
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
    "precio1": 1689.59,
    "precio2": 1500.87,
    "precio3": 1466.19,
    "precio4": 1441.71,
    "precio5": 1355,
    "preciopub": 1799.92,
    "unidad": "PZA  "
}
```
##### Response.result
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
    "precio1": 1689.59,
    "precio2": 1500.87,
    "precio3": 1466.19,
    "precio4": 1441.71,
    "precio5": 1355,
    "preciopub": 1799.92,
    "unidad": "PZA  "
}
 ```



---
### Actualizar Artículo
PUT /api/v3/articulos/:numart
```json
{
    "desc": "AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146",
    "impuesto1": 16.00,
    "divisa": "P"
}
```
```json
{"UPDATED"}
```



---
### Leer Artículo
GET /api/v3/articulos/:numart
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
    "precio1": 1689.59,
    "precio2": 1500.87,
    "precio3": 1466.19,
    "precio4": 1441.71,
    "precio5": 1355,
    "preciopub": 1799.92,
    "unidad": "PZA  "
}
```



---
### Leer Artículo por UPC 
GET /api/v3/articulos/upc/:upc

Leer un artículo, buscarlo usando el UPC (Código de barra).  
La respuesta igual que [Leer Artículo](#leer-artículo)


---
### Leer Artículo por UPC o Clave
GET /api/v3/articulos/cveupc/:txt

Leer un artículo, buscarlo usando el UPC, sino lo encuentra buscarlo por la clave *numart*.  
La respuesta igual que [Leer Artículo](#leer-artículo)


---
### Buscar Artículos

Buscar los artículos que cumplan las condiciones indicadas en las variables recibidas.

| Variable | Significado                                               |
| -------- | --------------------------------------------------------- |
| offset   | A partir de que registro iniciar búsqueda. Default 0      |
| limit    | Cuantos registros obtener. Default 100                    |
| order    | Orden deseado. Los disponibles son:updated,id,numart,desc |
| q        | Palabras a buscar dentro de la descripción.                                         |


| Ejemplo de Búsqueda                                | Ruta                                |
| -------------------------------------------------- | ----------------------------------- |
| Buscar los primeros 25 arts                        | /api/v3/articulos?limit=25          |
| Buscar articulos que contenga la palabra: *rotula* | /api/v3/articulos?q=rotula          |
| Buscar rótulas para Suburban                       | /api/v3/articulos?q=rotula+suburban |

GET /api/v3/articulos?condiciones

```json
[
{"id":1,"created":"2023-02-05 21:53:21","updated":"2023-02-06 15:23:57","numart":"           GRC-55657","desc":"AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146","activo":1,"categoria":"  SUS","clavesat":"25172000","codigo":"55657","divisa":"P","esmatpelig":0,"excento":0,"existencia":0,"familia":"SUS02","impuesto1":16,"impuesto2":0,"linea":"  114","marca":"GRC","matpelig":"","modelo":"","obs":"","precio1":1689.59,"precio2":1500.87,"precio3":1466.19,"precio4":1441.71,"precio5":1355,"preciopub":1799.92,"unidad":"PZA  "},
{"id":1,"created":"2023-02-05 21:53:21","updated":"2023-02-06 15:23:57","numart":"           GRC-55657","desc":"AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146","activo":1,"categoria":"  SUS","clavesat":"25172000","codigo":"55657","divisa":"P","esmatpelig":0,"excento":0,"existencia":0,"familia":"SUS02","impuesto1":16,"impuesto2":0,"linea":"  114","marca":"GRC","matpelig":"","modelo":"","obs":"","precio1":1689.59,"precio2":1500.87,"precio3":1466.19,"precio4":1441.71,"precio5":1355,"preciopub":1799.92,"unidad":"PZA  "},
{"id":1,"created":"2023-02-05 21:53:21","updated":"2023-02-06 15:23:57","numart":"           GRC-55657","desc":"AMORTIGUADOR DEL L = G55657 = MW872 = 400872 = 7000146","activo":1,"categoria":"  SUS","clavesat":"25172000","codigo":"55657","divisa":"P","esmatpelig":0,"excento":0,"existencia":0,"familia":"SUS02","impuesto1":16,"impuesto2":0,"linea":"  114","marca":"GRC","matpelig":"","modelo":"","obs":"","precio1":1689.59,"precio2":1500.87,"precio3":1466.19,"precio4":1441.71,"precio5":1355,"preciopub":1799.92,"unidad":"PZA  "},
]

```


