## Notas

Rutas de Notas

| Accion         | Ruta                                  |
| -------------- | ------------------------------------- |
| Leer por clave | GET  /api/v3/clientes/clave/:numdoc   |



Campos de las Notas

| Campo      | Significado                                                     |
| ---------- | --------------------------------------------------------------- |
| id         | Id incremental disponible solo através de SAIT API              |
| created    | Timestamp del momento de creación                               |
| updated    | Timestamp ultima actualizacion                                  |
| tipodoc    | Puede tener 2 caracteres que lo identifiquen, En este caso " N" |
| numdoc     | Clave de identificación del documento.                          |
| pagos      | Pagos realizados en caja. Cada línea representa un movimiento.  |
| formapago  | El tipo de pago a usar. 1.-Contado2.-Credito                    |
| fecha      | Fecha en que se proceso el documento                            |
| hora       | Hora en que se proceso el documento                             |
| divisa     | Tipo de divisa usada "P" (pesos) o "D" (dólares).               |
| tc         | Tipo de cambio usado                                            |
| importe    | Total del precio de los artículos (Precio base x Cantidad)      |
| descuento  | Descuento aplicado al importe                                   |
| impuesto1  | primer impuesto aplicado (IVA)                                  |
| impuesto2  | primer impuesto aplicado (IEPS)                                 |
| costo      | Suma de los costos de los artículos                             |
| costo2     | Suma de los últimos costos de los artículos                     |
| costopro   | Costo promedio                                                  |
| numalm     | Identificación de la sucursal donde se procesa el documento     |
| status     | Status del documento                                            |
| datoscfdi  | JSON con los datos del cfdi, relacion, metodos de pago, uso.    |
| numfact    | Clave de la factura                                             |
| items      | Slice de los movimientos asociados a la nota                    |

Campos de los Items

| Campo      | Significado                                                     |
| ---------- | --------------------------------------------------------------- |
| tipodoc    | Puede tener 2 caracteres que lo identifiquen, En este caso " N" |
| numdoc     | Clave de identificación del documento.                          |
| numpar     | Número de partida                                               |
| precio     | Precio del item                                                 |
| pjedesc    | Porcentaje de descuento                                         |
| impuesto1  | Porcentaje de impuesto1 (IVA)                                   |
| impuesto2  | Porcentaje de impuesto2 (IEPS)                                  |
| costo      | costo del item                                                  |
| costo2     | ultimo costo del item                                           |
| costopro   | Costo promedio del item                                         |
| cant       | Cantidad de artículos que se movieron                           |
| unidad     | Unidad en que se manejas los artículos                          |
| excento    |               |
| importe    |               |
| obs        |               |

---
### Leer Nota por Clave

GET /api/v3/notas/:numdoc

response:
```json
{
    "tipodoc"       : " N",
    "numdoc"        : "   B744000",
    "pagos"         : "^  B7655    5^EF^50.00^15.00^P^1^^",
    "formapago"     : "1",
    "fecha"         : "2024-06-10",
    "hora"          : "08:11:46",
    "divisa"        : "P",
    "tc"            : 16.35,
    "importe"       : 13.89,
    "descuento"     : 0,
    "impuesto1"     : 2.22,
    "impuesto2"     : 0,
    "costo"         : 2.2200000286102295,
    "costo2"        : 2.5899999141693115,
    "costopro"      : 0,
    "numalm"        : " 2",
    "subtotal"      : 0,
    "total"         : 0,
    "status"        : 0,
    "datoscfdi"     : "",
    "numfact"       : "",
    "items" : [
      {
        "tipodoc"   : " N",
        "numdoc"    : "   B744000",
        "numpar"    : "  1",
        "numart"    : "                SBTC",
        "precio"    : 2.7778,
        "pjedesc"   : 0,
        "impuesto1" : 16,
        "impuesto2" : 0,
        "costo"     : 2.2200000286102295,
        "costo2"    : 2.5920000076293945,
        "costopro"  : 0,
        "cant"      : 5,
        "unidad"    : "PZA",
        "exento"    : 0,
        "importe"   : 0,
        "obs"       : ""
      }
    ]
}
```
