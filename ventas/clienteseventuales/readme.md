# Clientes eventuales

**Rutas de Clientes**

| Accion     | Ruta                                        |
| ---------- | ------------------------------------------- |
| Crear      | POST   /api/v3/clienteseventuales           |
| Leer uno   | GET    /api/v3/clienteseventuales/:num      |
| Actualizar | PUT    /api/v3/clienteseventuales/:num      |
| Borrar     | DELETE /api/v3/clienteseventuales/:num      |
| Buscar     | GET    /api/v3/clienteseventuales?filters...|


**Campos de los Clientes Eventuales**

| Campo      | Significado                                                      |
| ---------- | ---------------------------------------------------------------- |
| id         | Id incremental disponible solo através de SAIT API               |
| created    | Timestamp del momento de creación                                |
| updated    | Timestamp ultima actualizacion                                   |
| numcliev   | Clave de cliente eventual C(10)AD                                |
| nomcliev   | Nombre cliente eventual                                          |
| tipope     | Tipo cliente eventual Persona, Empresa                           |
| possep     | Posición de separación, para nombre y apellido cuando es persona |
| calle      | Calle                                                            |
| numext     | Número exterior                                                  |
| colonia    | Colonia                                                          |
| ciudad     | Ciudad                                                           |
| estado     | Estado                                                           |
| cp         | Codigo Postal                                                    |
| pais       | Pais                                                             |
| rfc        | RFC                                                              |
| telefono   | Telefono                                                         |
| telefono2  | Telefono2                                                        |
| contacto   | Nombre del contacto                                              |
| email      | Correo electrónico del contacto                                  |
| ultvent    | Ultima venta                                                     |
| idregimen  |                                                                  |
| satapenom  | True: Apellido primero, False: Nombre primero                    |

---

## Create Cliente Eventual

Crea un nuevo cliente eventual registrandolo en la base de datos y genera un evento "MODCLI" para especificar en SaitDist que un nuevo cliente fue creado.

**ruta: /ventas/clienteseventuales**

Request:
```json
{
  "numcliev"  : "0-8",
  "nomcliev"  : "Morsa",
  "rfc"       : "ELO3210OL34R",
  "CP"        : "83754",
  "idregimen" : "601"
}
```

## Read Cliente Eventual

Retorna la informacion de un cliente eventual. La busqueda se realiza mediante el numcliev.

**ruta: /ventas/clienteseventuales/0-5**

response:
```json
"result":{
  "id": 4,
  "created": "2024-04-12 17:41:27",
  "updated": "2024-04-12 23:42:50",
  "numcliev": "0-5",
  "nomcliev": "Elotito Dev Support",
  "tipope": "E",
  "possep": 0,
  "calle": "",
  "numext": "",
  "colonia": "",
  "ciudad": "",
  "estado": "",
  "cp": "83754",
  "pais": "",
  "rfc": "ELO3210OL34R",
  "telefono": "",
  "telefono2": "",
  "contacto": "",
  "email": "",
  "ultvent": "2024-04-12",
  "idregimen": "601",
  "satapenom": false
},
"error": ""
```

## Update Cliente Eventual

Actualiza mediante el identificador **numcliev** un cliente eventual registrado en la base de datos y genera un evento "MODCLI" para especificar en SaitDist que la informacion de un cliente fue Actualizada.

**ruta: /ventas/clienteseventuales/0-9**

recibir numcliev para buscar al paciente que se va a actualizar
```go
cliEv.Numcliev = utils.AlignR("0-9", 10)
```

Request:
```json
{
  "nomcliev"  : "Morsa",
  "rfc"       : "ELO3210OL34R",
  "CP"        : "83754",
  "idregimen" : "601"
}
```

## Delete Cliente Eventual

Elimina un cliente eventual registrado en la base de datos y genera un evento "DELCLI" para especificar en SaitDist que el cliente fue eliminado.

**ruta: /ventas/clienteseventuales/0-9**

recibir numcliev para buscar al paciente que se va a borrar
```go
cliEv.Numcliev = utils.AlignR("0-9", 10)
```

Request:
```json
{
  "nomcliev"  : "Morsa",
  "rfc"       : "ELO3210OL34R",
  "CP"        : "83754",
  "idregimen" : "601"
}
```

## List Cliente Eventual

Retorna un listado de clientes eventuales, el resultado puede ser controlado dependiendo los filtros que se apliquen.

**ruta: /ventas/clienteseventuales?filtros**

| Variable | Significado                                             |
| -------- | ------------------------------------------------------- |
| offset   | A partir de que registro iniciar búsqueda. Default 0    |
| limit    | Cuantos registros obtener. Default 100                  |
| order    | Orden deseado. Disponibles:updated,id,numcli,nomcli,rfc |
| q        | Palabras a buscar                                       |
| estado   | filtro por estado del pais                              |
| rfc      | filtro por rfc del cliente                              |

response:
```json
"result":[
  {
    "id": 4,
    "created": "2024-04-12 17:41:27",
    "updated": "2024-04-12 23:42:50",
    "numcliev": "0-5",
    "nomcliev": "Elotito Dev Support",
    "tipope": "E",
    "possep": 0,
    "calle": "",
    "numext": "",
    "colonia": "",
    "ciudad": "",
    "estado": "",
    "cp": "83754",
    "pais": "",
    "rfc": "ELO3210OL34R",
    "telefono": "",
    "telefono2": "",
    "contacto": "",
    "email": "",
    "ultvent": "2024-04-12",
    "idregimen": "601",
    "satapenom": false
  },
  {...}
],
"error": ""
```