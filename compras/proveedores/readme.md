## Proveedores

| Accion                                | Ruta                                                                                                      |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------|
| [Consultar](#consultar)               | GET   /api/v3/proveedores/:pk                                                                             |
| [Listar](#listar)                     | GET   /api/v3/proveedores?filtros...                                                                      |
| [Resumen](#resumen)                   | POST  /api/v3/proveedores/:pk/resumen                                                                     |
| [Estado de cuenta](#estado-de-cuenta) | POST  /api/v3/proveedores/:pk/estado_de_cuenta?fecha1=..&fecha2=..&solo_con_saldo=true&limit=..&offset=.. |

> `:pk` corresponde al número de proveedor (`numprov`). Se alinea internamente a 5 caracteres en todas las rutas.

---

### Conceptos generales

Campos del registro (tabla `provedor`)
| Campo     | Tipo   | Significado                                        |
|-----------|--------|----------------------------------------------------|
| id        | int    | Identificador autoincrementable interno            |
| created   | string | Fecha de creación del registro                     |
| updated   | string | Fecha de la última modificación                    |
| numprov   | string | Llave primaria (PK), string de hasta 5 caracteres  |
| nomprov   | string | Nombre o razón social del proveedor                |
| calle     | string | Dirección: calle                                   |
| numext    | string | Dirección: número exterior                         |
| colonia   | string | Dirección: colonia                                 |
| ciudad    | string | Dirección: ciudad                                  |
| estado    | string | Dirección: estado                                  |
| cp        | string | Código postal                                      |
| telefono  | string | Teléfono principal                                 |
| fax       | string | Número de fax                                      |
| clasif    | string | Clasificación interna del proveedor                |
| compano   | float  | Monto de compras del año                           |
| ultcomp   | string | Fecha de la última compra                          |
| contacto  | string | Nombre del contacto principal                      |
| rfc       | string | Registro Federal de Contribuyentes (13 caracteres) |
| pjedesc   | float  | Porcentaje de descuento                            |
| saldo     | float  | Saldo actual con el proveedor                      |
| diascred  | float  | Días de crédito que otorga el proveedor            |
| numcta    | string | Número de cuenta contable                          |
| tproviva  | float  | Tipo de proveedor para efectos de IVA              |
| diotpais  | string | Código de país para la DIOT                        |
| diotnal   | string | Nacionalidad para la DIOT                          |
| diottaxid | string | Tax ID para extranjeros en la DIOT                 |
| email     | string | Correo electrónico principal                       |
| email2    | string | Correo electrónico secundario                      |
| email3    | string | Correo electrónico terciario                       |
| contacto2 | string | Contacto secundario                                |
| contacto3 | string | Contacto terciario                                 |
| telefono2 | string | Teléfono secundario                                |
| telefono3 | string | Teléfono terciario                                 |
| banco     | string | Nombre del banco                                   |
| cuentaban | string | Número de cuenta bancaria                          |
| claveban  | string | Clave interbancaria / clabe                        |
| obs       | string | Observaciones (texto libre)                        |
| cuentasb  | string | Cuentas bancarias adicionales                      |
| idregla   | float  | Regla de impuestos/contable asociada               |
| impuesto1 | float  | Tasa de impuesto por defecto                       |
| idregimen | string | Régimen fiscal del proveedor                       |

> Todos los campos, salvo `id`, `numprov`, `nomprov` y `compano`, son *nullable*: si el proveedor no tiene ese dato capturado, la API regresa `null` en vez de una cadena vacía.

---

### Consultar

GET /api/v3/proveedores/:pk

Obtiene un proveedor por su número de proveedor (`numprov`). Regresa el registro completo con todos los campos de la tabla `provedor` (equivale a `SELECT * FROM provedor WHERE numprov = :pk`).

`:pk` (obligatorio) corresponde al número de proveedor a consultar.

Response
```json
{
  "id": 45,
  "created": "2020-05-12 10:30:00",
  "updated": "2024-01-08 16:02:00",
  "numprov": "  123",
  "nomprov": "PROVEEDOR EJEMPLO SA DE CV",
  "calle": "AV INDUSTRIA",
  "numext": "450",
  "colonia": "CENTRO",
  "ciudad": "MEXICALI",
  "estado": "BC",
  "cp": "21000",
  "telefono": "6861234567",
  "fax": null,
  "clasif": "A",
  "compano": 185300.00,
  "ultcomp": "2024-01-05",
  "contacto": "JUAN PEREZ",
  "rfc": "PEEJ850101ABC",
  "pjedesc": 5.00,
  "saldo": 9536.00,
  "diascred": 30,
  "numcta": "2100-01",
  "tproviva": 1,
  "diotpais": null,
  "diotnal": null,
  "diottaxid": null,
  "email": "contacto@proveedor.com",
  "email2": null,
  "email3": null,
  "contacto2": null,
  "contacto3": null,
  "telefono2": null,
  "telefono3": null,
  "banco": "BBVA",
  "cuentaban": "0123456789",
  "claveban": "012180001234567895",
  "obs": null,
  "cuentasb": null,
  "idregla": 3,
  "impuesto1": 16.00,
  "idregimen": "601"
}
```

---

### Listar

GET /api/v3/proveedores?filtros...

Retorna los proveedores aplicando filtros opcionales. Solo trae las columnas `id, created, updated, numprov, nomprov, rfc, saldo` (no trae todos los campos del registro, a diferencia de [Consultar](#consultar)).

filtros
| Filtro | Descripcion                                                                                                                                      |
|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| q      | Busqueda de texto libre sobre `numprov`, `nomprov` y `rfc`. Se separa por espacios (hasta 4 palabras) y cada palabra debe coincidir parcialmente |
| limit  | Cantidad de registros a tomar                                                                                                                    |
| offset | Tomar registros a partir del registro X                                                                                                          |

Response
```json
[
  {
    "id": "45",
    "created": "2020-05-12 10:30:00",
    "updated": "2024-01-08 16:02:00",
    "numprov": "  123",
    "nomprov": "PROVEEDOR EJEMPLO SA DE CV",
    "rfc": "PEEJ850101ABC",
    "saldo": "9536.00"
  },
  {}
]
```

---

### Resumen

POST /api/v3/proveedores/:pk/resumen

Regresa el resumen general del proveedor: datos de contacto, saldo en moneda nacional, totales de cargos/abonos (calculados sobre `cxp`) y el adeudo más antiguo pendiente de pago. No recibe filtros.

`:pk` (obligatorio) corresponde al número de proveedor a consultar.

Campos
| Campo              | Tipo   | Significado                                                                                                               |
|--------------------|--------|---------------------------------------------------------------------------------------------------------------------------|
| numprov            | string | Número de identificación del proveedor                                                                                    |
| nomprov            | string | Nombre o razón social del proveedor                                                                                       |
| rfc                | string | Registro Federal de Contribuyentes                                                                                        |
| cp                 | string | Código postal                                                                                                             |
| estado             | string | Estado (dirección)                                                                                                        |
| ciudad             | string | Ciudad (dirección)                                                                                                        |
| telefono           | string | Teléfono principal                                                                                                        |
| contacto           | string | Nombre del contacto principal                                                                                             |
| emailcontacto      | string | Correo del contacto (`provedor.email2`)                                                                                   |
| diascred           | float  | Días de crédito que otorga el proveedor                                                                                   |
| saldomn            | float  | Saldo total en moneda nacional (cargos - abonos, importes en USD convertidos con `tc`)                                    |
| totalcargos        | float  | Suma de cargos (`ca='0'`) en moneda nacional                                                                              |
| totalabonos        | float  | Suma de abonos (`ca='1'`) en moneda nacional                                                                              |
| adeudo_mas_antiguo | object | El cargo con saldo pendiente más antiguo: `{ saldo, fecha, folio }`. Ausente en la respuesta si no hay adeudos pendientes |

Response
```json
{
  "numprov": "  123",
  "nomprov": "PROVEEDOR EJEMPLO SA DE CV",
  "rfc": "PEEJ850101ABC",
  "cp": "21000",
  "estado": "BC",
  "ciudad": "MEXICALI",
  "telefono": "6861234567",
  "contacto": "JUAN PEREZ",
  "emailcontacto": "contacto@proveedor.com",
  "diascred": "30",
  "saldomn": "9536.0000",
  "totalcargos": "185300.0000",
  "totalabonos": "175764.0000",
  "adeudo_mas_antiguo": {
    "saldo": "3200.00",
    "fecha": "2024-11-15",
    "folio": "  FA0004512"
  }
}
```

> Si el proveedor no existe (`numprov` sin registro en `provedor`), la ruta regresa error `"proveedor no encontrado"`.

---

### Estado de cuenta

POST /api/v3/proveedores/:pk/estado_de_cuenta?fecha1=..&fecha2=..&solo_con_saldo=true&limit=..&offset=..

Regresa el listado de movimientos (tabla `cxp`) del proveedor, con saldo acumulado calculado en orden cronológico (por `fechahora`).

`:pk` (obligatorio) corresponde al número de proveedor a consultar.

filtros
| Filtro         | Descripcion                                                                   |
|----------------|-------------------------------------------------------------------------------|
| fecha1         | (opcional) Fecha inicial del rango a consultar — filtra `cxp.fecha >= fecha1` |
| fecha2         | (opcional) Fecha final del rango a consultar — filtra `cxp.fecha <= fecha2`   |
| solo_con_saldo | (opcional) `"true"` filtra solo movimientos con saldo pendiente (`saldo > 0`) |
| limit          | (opcional) Cantidad de registros a tomar                                      |
| offset         | (opcional) Tomar registros a partir del registro X                            |

Campos
| Campo      | Tipo   | Significado                                                                     |
|------------|--------|---------------------------------------------------------------------------------|
| id         | int    | Identificador interno del movimiento                                            |
| keycxp     | string | Llave única del movimiento en `cxp`                                             |
| keydocum   | string | Llave del documento relacionado (compra, gasto, etc.)                           |
| keyrefer   | string | Llave de referencia del movimiento                                              |
| numdoc     | string | Número de documento interno                                                     |
| numdocprov | string | Número de documento / folio del proveedor                                       |
| fecha      | string | Fecha del movimiento                                                            |
| fechahora  | string | Fecha y hora exacta de captura del movimiento (usada para el orden cronológico) |
| ca         | string | Tipo de movimiento: `'0'` cargo, `'1'` abono                                    |
| conc       | string | Clave del concepto de movimiento                                                |
| concdesc   | string | Descripción del concepto (`conccxp.desc`); `"Sin descripcion"` si viene vacío   |
| cargo      | float  | Importe si el movimiento es un cargo (`ca='0'`), de lo contrario `0.00`         |
| abono      | float  | Importe si el movimiento es un abono (`ca='1'`), de lo contrario `0.00`         |
| divisa     | string | Divisa del movimiento (`'P'` pesos, cualquier otro valor: moneda extranjera)    |
| importe    | float  | Importe original del movimiento                                                 |
| moneda     | string | `"MXN"` o `"DLS"` según `divisa`                                                |
| tc         | float  | Tipo de cambio aplicado al movimiento                                           |
| saldodoc   | float  | Saldo pendiente del documento (`cxp.saldo`)                                     |
| saldoacum  | float  | Saldo acumulado de la cuenta hasta ese movimiento, en orden cronológico         |

Response
```json
[
  {
    "id": "1024",
    "keycxp": "  FA0004512",
    "keydocum": "  C0004512",
    "keyrefer": "",
    "numdoc": "4512",
    "numdocprov": "F-998877",
    "fecha": "2024-11-15",
    "fechahora": "20241115093000",
    "ca": "0",
    "conc": "CO",
    "concdesc": "COMPRA",
    "cargo": "3200.00",
    "abono": "0.00",
    "divisa": "P",
    "importe": "3200.00",
    "moneda": "MXN",
    "tc": "1.0000",
    "saldodoc": "3200.00",
    "saldoacum": "3200.0000"
  },
  {}
]
```