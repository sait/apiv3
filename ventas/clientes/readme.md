## Clientes

Rutas de Clientes

| Accion         | Ruta                                  |
| -------------- | ------------------------------------- |
| Crear          | POST /api/v3/clientes                 |
| Actualizar     | PUT  /api/v3/clientes/:id             |
| Leer por id    | GET  /api/v3/clientes/:id             |
| Leer por clave | GET  /api/v3/clientes/clave/:key      |
| Buscar         | GET  /api/v3/clientes?condiciones...  |


Campos de los Clientes

| Campo      | Significado                                                |
| ---------- | ---------------------------------------------------------- |
| id         | id incremental disponible solo através de SAIT API         |
| created    | timestamp del momento de creación                          |
| updated    | timestamp ultima actualizacion                             |
| numcli     | clave de cliente C(5)AD                                    |
| numvend    | clave del vendedor que atiende al cliente C(5)AD           |
| numzona    | clave de zona de ventas C(5)AD                             |
| clavecli   | clave adicional ampliada del cliente C(10)                 |
| nomcli     | nombre del cliente                                         |
| rfc        | registro federal de causantes del cliente                  |
| calle      | calle del domicilio                                        |
| numext     | número exterior del domicilio                              |
| numint     | número interior del domicilio                              |
| colonia    |                                                            |
| ciudad     |                                                            |
| estado     | nombre del estado                                          |
| cp         | código postal                                              |
| telefono   | teléfono del cliente                                       |
| fax        | aun existe ???                                             |
| clasif     | clasificación del cliente                                  |
| atvent     | nombre del contacto para asuntos de venta                  |
| email1     | email del contacto de venta                                |
| atcobr     | nombre del contacto para asuntos de cobranza               |
| email2     | email del contacto de cobranza                             |
| limcred    | límite de crédito en pesos                                 |
| diascred   | dias de crédito antes de considerar una cuenta como adeudo |
| saldo      | saldo actual del cliente en pesos                          |
| pjedesc    | porcentaje de descuento autorizado al cliente              |
| precioutil | lista de precio a usar de 1 a 5 C(1)                       |
| obs        | observaciones a mostrar al vendedor al momento de vender   |
| numcta     | numero de cuenta contable                                  |
| suspendido | boleano que indica credito suspendido al cliente           |
| permitecod | boleano que indica si se permite entrega o devuelvase      |
| metodopago | metodo de pago para CFDI                                   |
| usocfdi    | clave fiscal del uso de CFDI                               |
| idregimen  | clave del regimen fiscal del cliente                       |
| emailtw    | email de acceso para tienda web                            |


### Crear Cliente

POST /api/v3/clientes

request:
```json
{
    "numcli":"10",
    "nomcli":"Bimbo de Mexico",
    "calle":"Mimosas",
    "numext":"117",
    "colonia":"Santa María Insurgente",
    "ciudad":"México",
    "estado":"CDMX",
    "cp":"06430"
}
```

response:
```json
{
    "id":1,
    "created":"2023-02-07 21:37:46",
    "updated":"2023-02-07 21:40:08",
    "numcli":"10",
    "nomcli":"Bimbo de Mexico",
    "calle":"Mimosas",
    "numext":"117",
    "colonia":"Santa María Insurgente",
    "ciudad":"México",
    "estado":"CDMX",
    "cp":"06430"
}
```

### Actualizar Cliente

PUT /api/v3/clientes/:id

request:
```json
{
    "nomcli":"Bimbo de Mexico",
    "calle":"Mimosas",
    "numext":"117",
}
```

response:
```json
"UPDATED"
```

### Leer Cliente por Id

GET /api/v3/clientes/:id

response:
```json
{
    "id":1,
    "created":"2023-02-07 21:37:46",
    "updated":"2023-02-07 21:40:08",
    "numcli":"10",
    "nomcli":"Bimbo de Mexico",
    "calle":"Mimosas",
    "numext":"117",
    "colonia":"Santa María Insurgente",
    "ciudad":"México",
    "estado":"CDMX",
    "cp":"06430"
}
```

### Leer Cliente por Clave

GET /api/v3/clientes/clave/:clave

response:
```json
{
    "id":1,
    "created":"2023-02-07 21:37:46",
    "updated":"2023-02-07 21:40:08",
    "numcli":"10",
    "nomcli":"Bimbo de Mexico",
    "calle":"Mimosas",
    "numext":"117",
    "colonia":"Santa María Insurgente",
    "ciudad":"México",
    "estado":"CDMX",
    "cp":"06430"
}
```

### Buscar Clientes

GET /api/v3/clientes?variables

| Variable | Significado                                             |
| -------- | ------------------------------------------------------- |
| offset   | A partir de que registro iniciar búsqueda. Default 0    |
| limit    | Cuantos registros obtener. Default 100                  |
| order    | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc |
| q        | Palabras a buscar                                       |
| estado   | filtro por estado del pais                              |
| numvend  | filtro por vendedor que atiende a cliente               |
| numzon   | filtro por zona de clientes                             |


| Ejemplo de Búsqueda                      | Ruta                                       |
| ---------------------------------------- | ------------------------------------------ |
| Clientes con nombre ignacio              | /api/v3/clientes?q=ignacio                 |
| Clientes con nombre ignacio gonzalez     | /api/v3/clientes?q=ignacio+gonzalez        |
| Clientes de Colima                       | /api/v3/clientes?estado=colima             |
| Clientes de Colimna con nombre Industria | /api/v3/clientes?estado=colima&q=industria |
| 20 ultimos clientes modificados          | /api/v3/clientes?limit=20&order=-updated   |


