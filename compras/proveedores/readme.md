## Proveedores

| Accion                  | Ruta                                 |
|-------------------------|--------------------------------------|
| [Consultar](#consultar) | GET   /api/v3/proveedores/:pk        |
| [Listar](#listar)       | GET   /api/v3/proveedores?filtros... |

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

---

### Consultar

GET /api/v3/proveedores/:pk

Obtiene un proveedor por su número de proveedor (`numprov`). Regresa el registro completo con todos los campos de la tabla `provedor`.

`:pk` (obligatorio) corresponde al número de proveedor a consultar. Se alinea internamente a 5 caracteres.

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
  "fax": "",
  "clasif": "A",
  "compano": "185300.00",
  "ultcomp": "2024-01-05",
  "contacto": "JUAN PEREZ",
  "rfc": "PEEJ850101ABC",
  "pjedesc": "5.00",
  "saldo": "9536.00",
  "diascred": "30",
  "numcta": "2100-01",
  "tproviva": "1",
  "diotpais": "",
  "diotnal": "",
  "diottaxid": "",
  "email": "contacto@proveedor.com",
  "email2": "",
  "email3": "",
  "contacto2": "",
  "contacto3": "",
  "telefono2": "",
  "telefono3": "",
  "banco": "BBVA",
  "cuentaban": "0123456789",
  "claveban": "012180001234567895",
  "obs": "",
  "cuentasb": "",
  "idregla": "3",
  "impuesto1": "16.00",
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