## Clientes

Rutas de Clientes

| Accion                            | Ruta                                   |
|-----------------------------------|----------------------------------------|
| [Crear](#crear-cliente)           | POST   /api/v3/clientes                |
| [Leer](#leer-cliente)             | GET    /api/v3/clientes/:numcli        |
| [Actualizar](#actualizar-cliente) | PUT    /api/v3/clientes/:numcli        |
| [Borrar](#borrar-cliente)         | DELETE /api/v3/clientes/:numcli        |
| [Buscar](#buscar-clientes)        | GET    /api/v3/clientes?condiciones... |


Campos de los Clientes

| Campo      | Tipo     | Significado                                                |
|------------|----------|------------------------------------------------------------|
| id         | int      | id incremental disponible solo através de SAIT API         |
| created    | datetime | timestamp del momento de creación                          |
| updated    | datetime | timestamp ultima actualizacion                             |
| numcli     | varchar  | clave de cliente C(5)AD                                    |
| nomcli     | varchar  | nombre del cliente                                         |
| numvend    | varchar  | clave del vendedor que atiende al cliente C(5)AD           |
| numzona    | varchar  | clave de zona de ventas C(5)AD                             |
| clavecli   | varchar  | clave adicional ampliada del cliente C(10)                 |
| rfc        | varchar  | registro federal de causantes del cliente                  |
| calle      | varchar  | calle del domicilio                                        |
| numext     | varchar  | número exterior del domicilio                              |
| numint     | varchar  | número interior del domicilio                              |
| colonia    | varchar  |                                                            |
| ciudad     | varchar  |                                                            |
| estado     | varchar  | nombre del estado                                          |
| cp         | varchar  | código postal                                              |
| telefono   | varchar  | teléfono del cliente                                       |
| fax        | varchar  | aun existe ???                                             |
| clasif     | varchar  | clasificación del cliente                                  |
| atvent     | varchar  | nombre del contacto para asuntos de venta                  |
| email1     | varchar  | email del contacto de venta                                |
| atcobr     | varchar  | nombre del contacto para asuntos de cobranza               |
| email2     | varchar  | email del contacto de cobranza                             |
| limcred    | decimal  | límite de crédito en pesos                                 |
| diascred   | decimal  | dias de crédito antes de considerar una cuenta como adeudo |
| saldo      | decimal  | saldo actual del cliente en pesos                          |
| pjedesc    | decimal  | porcentaje de descuento autorizado al cliente              |
| precioutil | char     | lista de precio a usar de 1 a 5 C(1)                       |
| obs        | text     | observaciones a mostrar al vendedor al momento de vender   |
| numcta     | varchar  | numero de cuenta contable                                  |
| suspendido | tinyint  | boleano que indica credito suspendido al cliente           |
| permitecod | tinyint  | boleano que indica si se permite entrega o devuelvase      |
| metodopago | text     | metodo de pago para CFDI                                   |
| usocfdi    | char     | clave fiscal del uso de CFDI                               |
| idregimen  | char     | clave del regimen fiscal del cliente                       |
| emailtw    | varchar  | email de acceso para tienda web                            |

---
### Crear Cliente

POST /api/v3/clientes

request:
```json
{
  "numcli": "  AF8",
  "nomcli": "SERVICIOS Y MAQUILADOS INTERNACIONALES",
  "calle": "CHURUBUSCO",
  "numext": "660",
  "colonia": "CUAUHTEMOC ESTE",
  "ciudad": "TECATE",
  "estado": "BAJA CALIFORNIA",
  "cp": "21470",
  "telefono": "665 654 25 71",
  "fax": "",
  "clasif": "",
  "atvent": "",
  "atcobr": "",
  "email1": "compras@smimx.net, Vazquez@smimx.net",
  "email2": "",
  "rfc": "SMI890804PH2",
  "limcred": 50000,
  "pjedesc": 0,
  "diascred": 15,
  "precioutil": "5",
  "recepfac": "",
  "pagofac": "",
  "obs": "",
  "numvend": "",
  "direnvio": "",
  "otrosdatos": "",
  "impuesto1": 0,
  "retencion1": 0,
  "retencion2": 0,
  "pais": "",
  "clavecli": "",
  "curp": "",
  "nomcomer": "",
  "numzona": "  CSF",
  "numint": "",
  "usocfdi": "",
  "formapago": "",
  "condpago": "",
  "emailtw": "",
  "idregimen": "601"
}
```

response:
```json
{
  "id": 81260,
  "created": "2024-10-31 00:38:45",
  "updated": "2024-10-31 00:38:45",
  "numcli": "  AF8",
  "nomcli": "SERVICIOS Y MAQUILADOS INTERNACIONALES",
  "calle": "CHURUBUSCO",
  "numext": "660",
  "colonia": "CUAUHTEMOC ESTE",
  "ciudad": "TECATE",
  "estado": "BAJA CALIFORNIA",
  "cp": "21470",
  "telefono": "665 654 25 71",
  "fax": "",
  "clasif": "",
  "ventano": 398062.8,
  "ultvent": "2024-06-19",
  "atvent": "",
  "atcobr": "",
  "email1": "compras@smimx.net, Vazquez@smimx.net",
  "email2": "",
  "rfc": "SMI890804PH2",
  "limcred": 50000,
  "saldo": 1166.76,
  "pjedesc": 0,
  "diascred": 15,
  "precioutil": "5",
  "recepfac": "",
  "pagofac": "",
  "obs": "",
  "numcta": "102-101-S01",
  "uid": 1215,
  "numvend": "",
  "obligareq": 1,
  "suspendido": 0,
  "direnvio": "",
  "otrosdatos": "",
  "impuesto1": 0,
  "retencion1": 0,
  "retencion2": 0,
  "permitecod": 0,
  "llavecred": 0,
  "pais": "",
  "clavecli": "",
  "curp": "",
  "nomcomer": "",
  "cfgdatdoc": "",
  "datosfe": "",
  "statusweb": 0,
  "claveweb": "",
  "numzona": "  CSF",
  "metodopago": "",
  "metodousar": "",
  "numint": "",
  "usocfdi": "",
  "formapago": "",
  "implocal": "",
  "condpago": "",
  "emailtw": "",
  "numidtrib": "",
  "idregimen": "601",
  "rec": 0
}
```


---
### Leer Cliente

GET /api/v3/clientes/AF8

response:
```json
{
  "id": 81260,
  "created": "2024-10-31 00:38:45",
  "updated": "2024-10-31 00:38:45",
  "numcli": "  AF8",
  "nomcli": "SERVICIOS Y MAQUILADOS INTERNACIONALES",
  "calle": "CHURUBUSCO",
  "numext": "660",
  "colonia": "CUAUHTEMOC ESTE",
  "ciudad": "TECATE",
  "estado": "BAJA CALIFORNIA",
  "cp": "21470",
  "telefono": "665 654 25 71",
  "fax": "",
  "clasif": "",
  "ventano": 398062.8,
  "ultvent": "2024-06-19",
  "atvent": "",
  "atcobr": "",
  "email1": "compras@smimx.net, Vazquez@smimx.net",
  "email2": "",
  "rfc": "SMI890804PH2",
  "limcred": 50000,
  "saldo": 1166.76,
  "pjedesc": 0,
  "diascred": 15,
  "precioutil": "5",
  "recepfac": "",
  "pagofac": "",
  "obs": "",
  "numcta": "102-101-S01",
  "uid": 1215,
  "numvend": "",
  "obligareq": 1,
  "suspendido": 0,
  "direnvio": "",
  "otrosdatos": "",
  "impuesto1": 0,
  "retencion1": 0,
  "retencion2": 0,
  "permitecod": 0,
  "llavecred": 0,
  "pais": "",
  "clavecli": "",
  "curp": "",
  "nomcomer": "",
  "cfgdatdoc": "",
  "datosfe": "",
  "statusweb": 0,
  "claveweb": "",
  "numzona": "  CSF",
  "metodopago": "",
  "metodousar": "",
  "numint": "",
  "usocfdi": "",
  "formapago": "",
  "implocal": "",
  "condpago": "",
  "emailtw": "",
  "numidtrib": "",
  "idregimen": "601",
  "rec": 0
}
```

---
### Actualizar Cliente

PUT /api/v3/clientes/AF8

request:
```json
{
  "nomcli": "MODIFICACION PRUEBA",
  "calle": "CHURUBUSCO",
  "numext": "660",
  "colonia": "CUAUHTEMOC ESTE",
  "ciudad": "TECATE",
  "estado": "BAJA CALIFORNIA",
  "cp": "21470",
  "telefono": "665 654 25 71",
  "fax": "",
  "clasif": "",
  "atvent": "",
  "atcobr": "",
  "email1": "compras@smimx.net, Vazquez@smimx.net",
  "email2": "",
  "rfc": "SMI890804PH2",
  "limcred": 50000,
  "pjedesc": 0,
  "diascred": 15,
  "precioutil": "5",
  "recepfac": "",
  "pagofac": "",
  "obs": "",
  "numvend": "",
  "direnvio": "",
  "otrosdatos": "",
  "impuesto1": 0,
  "retencion1": 0,
  "retencion2": 0,
  "pais": "",
  "clavecli": "",
  "curp": "",
  "nomcomer": "",
  "numzona": "  CSF",
  "numint": "",
  "usocfdi": "",
  "formapago": "",
  "condpago": "",
  "emailtw": "",
  "idregimen": "601",
}
```

response:
```json
"UPDATED"
```

---
### Borrar Cliente

DELETE api/v3/clientes/AF8

Request:
```json
"DELETED"
```

---
### Buscar Clientes

GET /api/v3/clientes?variables

| Variable | Significado                                             |
|----------|---------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0    |
| limit    | Cuantos registros obtener. Default 100                  |
| order    | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc |
| q        | Palabras a buscar                                       |
| estado   | filtro por estado del pais                              |
| rfc      | filtro por rfc del cliente                              |
| numvend  | filtro por vendedor que atiende a cliente               |
| numzon   | filtro por zona de clientes                             |
| emailtw  | filtro por emailtw de clientes                          |

| Ejemplo de Búsqueda                      | Ruta                                       |
|------------------------------------------|--------------------------------------------|
| Clientes con nombre ignacio              | /api/v3/clientes?q=ignacio                 |
| Clientes con nombre ignacio gonzalez     | /api/v3/clientes?q=ignacio+gonzalez        |
| Clientes de Colima                       | /api/v3/clientes?estado=colima             |
| Clientes de Colimna con nombre Industria | /api/v3/clientes?estado=colima&q=industria |
| 20 ultimos clientes modificados          | /api/v3/clientes?limit=20&order=-updated   |

response:
```json
[
    {
        "id": 81260,
        "created": "2024-10-31 00:38:45",
        "updated": "2024-10-31 00:38:45",
        "numcli": "  AF8",
        "nomcli": "SERVICIOS Y MAQUILADOS INTERNACIONALES",
        "calle": "CHURUBUSCO",
        "numext": "660",
        "colonia": "CUAUHTEMOC ESTE",
        "ciudad": "TECATE",
        "estado": "BAJA CALIFORNIA",
        "cp": "21470",
        "telefono": "665 654 25 71",
        "fax": "",
        "clasif": "",
        "ventano": 398062.8,
        "ultvent": "2024-06-19",
        "atvent": "",
        "atcobr": "",
        "email1": "compras@smimx.net, Vazquez@smimx.net",
        "email2": "",
        "rfc": "SMI890804PH2",
        "limcred": 50000,
        "saldo": 1166.76,
        "pjedesc": 0,
        "diascred": 15,
        "precioutil": "5",
        "recepfac": "",
        "pagofac": "",
        "obs": "",
        "numcta": "102-101-S01",
        "uid": 1215,
        "numvend": "",
        "obligareq": 1,
        "suspendido": 0,
        "direnvio": "",
        "otrosdatos": "",
        "impuesto1": 0,
        "retencion1": 0,
        "retencion2": 0,
        "permitecod": 0,
        "llavecred": 0,
        "pais": "",
        "clavecli": "",
        "curp": "",
        "nomcomer": "",
        "cfgdatdoc": "",
        "datosfe": "",
        "statusweb": 0,
        "claveweb": "",
        "numzona": "  CSF",
        "metodopago": "",
        "metodousar": "",
        "numint": "",
        "usocfdi": "",
        "formapago": "",
        "implocal": "",
        "condpago": "",
        "emailtw": "",
        "numidtrib": "",
        "idregimen": "601",
        "rec": 0
    },
    {}...
]
```