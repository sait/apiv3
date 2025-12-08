## Articulos

Rutas de Artículos

| Acción                                | Ruta                              |
|---------------------------------------|-----------------------------------|
| [Leer por clave](#leer-por-numdoc)    | GET  /api/v3/articulos/:numart    |
| [Buscar articulos](#buscar-articulos) | GET  /api/v3/articulos?filters... |

| Campo      | tipo     | Significado                                            |
|------------|----------|--------------------------------------------------------|
| id         | int      | id incremental disponible solo através de SAIT API     |
| created    | datetime | timestamp del momento de creación                      |
| updated    | datetime | timestamp ultima actualizacion                         |
| numart     | varchar  | clave del articulo (Obligatorio)                       |
| desc       | varchar  | descripción del articulo (Obligatorio)                 |
| obs        | text     | descripción larga u observaciones del artículo         |
| linea      | varchar  | clave de linea para clasificacion                      |
| familia    | varchar  | clave de familia para clasificacion                    |
| categoria  | varchar  | clave de categoria para clasifiacion                   |
| activo     | tinyint  | boleano para indicar si articulo está activo           |
| marca      | varchar  |                                                        |
| modelo     | varchar  |                                                        |
| unidad     | varchar  | unidad de venta: PZ,CJ,MTS (Obligatorio)               |
| precio1    | decimal  | precio segun lista 1 sin impuestos                     |
| precio2    | decimal  | precio segun lista 2 sin impuestos                     |
| precio3    | decimal  | precio segun lista 3 sin impuestos                     |
| precio4    | decimal  | precio segun lista 4 sin impuestos                     |
| precio5    | decimal  | precio segun lista 5 sin impuestos                     |
| preciopub  | decimal  | precio publico con impuestos incluidos                 |
| divisa     | char     | moneda para precio P=Pesos D=Dolar                     |
| ultcosto   | decimal  | ultimo costo de compra                                 |
| clavesat   | varchar  | clave segun SAT (Obligatorio)                          |
| excento    | tinyint  | boleando indicando si es excento de impuestos para SAT |
| impuesto1  | decimal  | % de IVA segun SAT                                     |
| impuesto2  | decimal  | % de IEPS segun SAT                                    |
| esmatpelig | tinyint  | Es Material Peligroso segun SAT                        |
| matpelig   | varchar  | Clave SAT para Material Peligroso                      |
| existencia | decimal  | Existencia TOTAL sumando TODAS las sucursales          |

### Leer por numdoc
GET /api/v3/articulos/:numart

example: /api/v3/articulos/1040

response:
```json
{
    "id": 1054,
    "created": "2024-06-29 12:18:40",
    "updated": "2025-12-01 16:59:17",
    "numalm": "",
    "numart": "                1040",
    "desc": "DICCIONARIO LAROUSSE ENCICLOPEDIA USUAL",
    "marca": "LAROUSSE",
    "modelo": "PREPARATORIA",
    "preciopub": 185,
    "clavesat": "60102300",
    "aplicacion": "",
    "codigo": "9789706073594",
    "unidad": "PZA",
    "unidefa": "",
    "linea": "11039",
    "familia": "  113",
    "categoria": " 1109",
    "numdep": "11",
    "valdep": " 11",
    "ubica": "",
    "series": 0,
    "impuesto1": 0,
    "impuesto2": 0,
    "numprov": "",
    "numprov1": "",
    "numprov2": "",
    "numprov3": "",
    "ultcomp": "2025-09-04",
    "ultcomp1": "",
    "ultcomp2": "",
    "ultcomp3": "",
    "ultvent": "2023-08-14",
    "existencia": 95,
    "minimo": 1,
    "maximo": 3,
    "reorden": 0,
    "divisa": "P",
    "precio1": 185,
    "precio2": 151,
    "precio3": 169,
    "precio4": 141,
    "precio5": 141,
    "factor1": 2.3046000003814697,
    "factor2": 1.9107999801635742,
    "factor3": 2.142400026321411,
    "factor4": 1.7719000577926636,
    "factor5": 1.7719000577926636,
    "ultcosto": 86.34600067138672,
    "ultcosto1": 0,
    "ultcosto2": 0,
    "ultcosto3": 0,
    "maxcosto": 86.3499984741211,
    "Costoactua": 0,
    "Costopro": 0,
    "Ventano": 0,
    "Ventanoqty": 0,
    "activo": 1,
    "Ultmaxcost": 0,
    "Ultactcost": 0,
    "Compano": 0,
    "Companoqty": 0,
    "Repyy": 0,
    "cant_defa": 0,
    "excento": 0,
    "preciof": 0,
    "servicio": 0,
    "fechamod": "2024-11-29",
    "obs": "",
    "usacaduc": 0,
    "usalotes": 0,
    "ventacorte": 0,
    "eskit": 0,
    "uid": 0,
    "otrosdatos": "",
    "pjedesc": 0,
    "oferta": 120,
    "insumo": 0,
    "peso": 0,
    "largo": 0,
    "ancho": 0,
    "altura": 0,
    "cantxcj": 0,
    "foto": "",
    "fotos": "",
    "idmarca": "212",
    "preciov2": 151,
    "preciov3": 141,
    "preciov4": 0,
    "preciov5": 0,
    "ppubv2": 163.08,
    "ppubv3": 152.28,
    "ppubv4": 0,
    "ppubv5": 0,
    "vol2": 0,
    "vol3": 0,
    "vol4": 0,
    "vol5": 0,
    "statusweb": 1,
    "implocal": 0,
    "fracaranc": "",
    "matpelig": "",
    "embalaje": "",
    "guiaid": "",
    "guiaiddesc": "",
    "esmatpelig": 0,
    "fraccok": 0,
    "imagenes": null,
    "unidades": null
  }
```

### Buscar articulos

GET /api/v3/articulos?filtros...

**filtros**
| filtro    | Significado                                                |
|-----------|------------------------------------------------------------|
| offset    | A partir de que registro iniciar búsqueda. Default 0       |
| limit     | Cuantos registros obtener. Default 100                     |
| order     | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc    |
| divisa    | P = pesos - D = Dolares                                    |
| statusweb | 0 = no disponible en tienda web - 1 = disponible en tienda |
| categoria | numero de categoria de articulo                            |
| familia   | numero de familia de articulo                              |
| linea     | numero de linea de articulo                                |
| numprov   | numero de proveedor                                        |
| numdep    | numero de departamento                                     |
| marca     | marca del articulo                                         |



response:
```json
[
    {
      "numart": "                  SE",
      "desc": "SOLICITUD DE EMPLEO PZA",
      "preciopub": 3,
      "clavesat": "14111800",
      "codigo": "",
      "unidad": "PZA",
      "linea": "14039",
      "familia": "  123",
      "categoria": " 1408",
      "numdep": "14",
      "numprov": "",
      "existencia": -20785,
      "divisa": "P",
      "activo": 1,
      "statusweb": 1,
      "imagenes": null,
      "unidades": null
    },
    {}...
]
```