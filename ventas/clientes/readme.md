## Clientes

Rutas de Clientes

| Accion                            | Ruta                                   |
|-----------------------------------|----------------------------------------|
| [Leer](#leer-cliente)             | GET    /api/v3/clientes/:numcli        |
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
### Leer cliente
GET /api/v3/clientes/:numcli

example: /api/v3/clientes/G2

response:
```json
{
    "id": 0,
    "created": "",
    "updated": "",
    "numcli": "   G2",
    "nomcli": "MINISO MEXICO",
    "calle": "MANUEL AVILA CAMACHO",
    "numext": "118",
    "colonia": "LOMAS DE CHAPULTEPEC V SECCION",
    "ciudad": "MIGUEL HIDALGO",
    "estado": "CIUDAD DE MEXICO",
    "pais": "MEXICO",
    "cp": "11000",
    "telefono": "6646421154",
    "telefono2": "",
    "email": "sendero.tijuana@miniso.com.mx",
    "rfc": "MME160812J15",
    "idregimen": "601",
    "nomcomer": "MINISO MEXICO",
    "numcta": "1008-XXXX",
    "clasif": "",
    "numzona": "  CSF",
    "usocfdi": "G03",
    "formapago": "",
    "condpago": "",
    "uid": 77,
    "retencion1": 0,
    "atvent": "",
    "email1": "sendero.tijuana@miniso.com.mx",
    "diascred": 0,
    "limcred": 0,
    "precioutil": "1",
    "pjedesc": 0,
    "numvend": "",
    "atcobr": "",
    "email2": "",
    "emailtw": "",
    "obligareq": 0,
    "permitecod": 0,
    "llavecred": 0,
    "suspendido": 0,
    "saldo": 0,
    "ventano": 0,
    "ultvent": "2024-09-23",
    "obs": "",
    "otrosdatos": ""
}
```

---
### Buscar clientes

GET /api/v3/clientes?filtros...

**filtros**
| filtro  | Significado                                             |
|---------|---------------------------------------------------------|
| offset  | A partir de que registro iniciar búsqueda. Default 0    |
| limit   | Cuantos registros obtener. Default 100                  |
| order   | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc |
| q       | Palabras a buscar                                       |
| estado  | filtro por estado del pais                              |
| rfc     | filtro por rfc del cliente                              |
| numvend | filtro por vendedor que atiende a cliente               |
| numzon  | filtro por zona de clientes                             |

response:
```json
[
    {
        "id": 1,
        "created": "2024-06-29 12:36:46",
        "updated": "2025-11-27 09:00:22",
        "numcli": "    0",
        "nomcli": "C O N T A D O",
        "calle": "HIDALGO",
        "numext": "183 OTE",
        "colonia": "CENTRO",
        "ciudad": "TECATE",
        "estado": "BAJA CALIFORNIA",
        "pais": "MEXICO",
        "cp": "21400",
        "telefono": "665-654-13-74",
        "telefono2": "",
        "email": "",
        "rfc": "XAXX010101000",
        "nomcomer": "",
        "numzona": "  CSF",
    },
    {}...
]
```