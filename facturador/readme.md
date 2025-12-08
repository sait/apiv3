# Facturador 

## Rutas
- [ ] GET  /facturador/notas/:numdoc
- [ ] GET  /facturador/rfcs/:rfc
- [ ] POST /facturador/facturas
- [ ] GET  /facturador/download/pdf
- [ ] GET  /facturador/download/xml
- [ ] pkg  /ventas/clientes: list()
- [ ] pkg  /ventas/clienteseventuales: list() create() update()
- [ ] pkg  /ventas/facturas create() get() print()
- [ ] pkg  /ventas/devventa create()
- [ ] pkg  /sat/cfdi create() print()


Seguridad:
- Los 3 primeros metodos deben enviar el header X-facturadortoken: FolioNota-CodigoFacturar
- En la API se valida que header sea valido, caso contrario regresa 400 : {"result": null, "error": "Codigo de Facturacion Incorrecto."}

## Tablas


### facturador_notas

- Contiene las notas que han sido facturadas usando el Facturador
- Es la base para el proceso "Emisor de Notas de Credito"
- En SAIT.exe se usara esta tabla para impedir facturar una nota que aparezca en esta tabla

```
| id  | created | updated | folionota | foliofac | uuidfac    | foliofacg | uuidfacg | folionc | uuidnc |
| --- | ------- | ------- | --------: | -------: | ---------- | --------- | -------- | ------- | ------ |
| 1   |         |         |     MXL10 |    AF102 | 3989872987 |           |          |         |        |
| 2   |         |         |           |          |            |           |          |         |        |
```

## Emision de Notas de Credito
Este punto es muy importante porque las facturas emitidas NO deben generar nuevos ingresos para la empresa.
Hay 2 casos, dependiendo el momento que el cliente se mete a facturar
1. La nota, ya fue incluida en una "Factura Global del Dia"
2. La nota aun no ha sido incluida en una "Factura Global del Dia"

Por practicidad se decidio que cada Factura emitida por el Facturador, tenga una Nota de Credito asociada. 

Para eso se creo un Proceso Especial llamado **"Emisor de Notas de Credito"** con el sigiente algoritmo: 
- Ciclo infinito
- Para todas las notas facturadas pendiente de asociar NC
	- Si la nota, ya tiene "Factura Globara del Dia" asociada
		- Emitir NC relacionando la Factura emitira a Cliente



## Periodo permitido para Facturar
Al parecer son 10 dias posteriores a la nota

## Vistas Frontend

```
Paso1
----
Folio de Nota: [   ]
Codigo para Facturar:[   ]
<Continuar>
----
Notas:
- Verifica que el "Codigo para Facturar" sea valido



Paso2
----
Ingresar datos fiscales
RFC         [       ]
Nombre      [       ]
RegFiscal   [       ]
UsoCfdi     [       ]
Cod Postal  [       ]
Correo      [       ]
<Continuar>
-----
Notas:
- Despues de escribir el RFC, la API regresa los RFC existentes buscando en clientes y cliEvent
- Si el RFC escrito existe en SAIT una vez, lo carga
- Si el RFC existe con varios CP en SAIT, muestra un modal para seleccionar
- Si RFC no existe, te deja teclear todo
- Los campos de UsoCfdi,CodigoPostal y Correo, siempre estan abiertos
- Si es cliente normal, se carga email1 e email2
- Al dar clic en <Continuar> prevalidar datos fiscales en satstatus.sait.mx/validarrecptor



Paso3
-----
Mostrar las partidas de la nota
Mostrar totales de la factura
<Facturar>
----
Notas:
- Deberia mostrar datos de paso 2
- Emite la factura
- Si RFC,CP existe en Clientes
    - Usar ese numcli
- Si RFC,CP existe en CliEvent
    - Actualizar Nombre,RegFiscal,Email
    - usar ese numcliev
- Sino
    - Crear RFC en CliEvent
    - Usar ese NumCliEv




Paso4
-----
Se ha enviado su factura a los correos: aaa,bbb,ccc
<Descargar PDF>
<Descargar XML> 
----
Notas:
	- Permite la descarga ahi mismo

```


## Buscar Notas

### GET  /facturador/notas/:numdoc

Busca el documento de Nota  en docum a traves de numdoc,
si el documento existe, tambien retornamos los items del documento
obtenidos desde la tabla Movim + la descripcion desde Arts.

Ejemplo: /facturador/notas/A139

**Encabezado - Header** 

```json 
    headers: { "X-facturadortoken" : "nota-codigofact" }
```

Response:
```json
result:[
    {
        "formapagosat"  : "01", 
        "metodopagosat" : "PUE", 
        "importe"       : 224.18, 
        "descuento"     : 0, 
        "subtotal"      : 0, 
        "impuesto1"     : 35.87, 
        "impuesto2"     : 0, 
        "total"         : 0, 
        "items" : [
            {
                "cant"      : 1, 
                "desc"      : "LIMPIADOR FLASH FLORAL 15/1000 ML distribuido",
                "unidad"    : "CAJA", 
                "pjedesc"   : 0, 
                "impuesto1" : 16, 
                "impuesto2" : 0, 
                "precio"    : 180.18103, 
                "importe"   : 0, 
            },
            {
                "cant"      : 1, 
                "desc"      : "ABRECUBETAS dist",
                "unidad"    : "PZA", 
                "pjedesc"   : 0, 
                "impuesto1" : 16, 
                "impuesto2" : 0, 
                "precio"    : 44, 
                "importe"   : 0, 
            }
        ]
    }
]
```

## Buscar RFC

### GET  /facturador/rfcs/:rfc

Busca en clientes y en cliEvent los clientes con el RFC indicado.
Los junta en un solo matriz, identificando en "tipo" la tabla origen.

**Encabezado - Header** 
```json 
    headers: { "X-facturadortoken" : "nota-codigofact" } 
```

Response
```json
result:[
    {
       "tipo"     : "clientes",
        "numcli"  : "89",
        "rfc"     : "BIM011108DJ5 ",
        "nomcli"  : "BIMBO",
        "email"   : "compras@bimbo.com",
        "calle"   : "MIMOSAS",
        "numext"  : "117",
        "colonia" : "INSURGENTES",
        "ciudad"  : "MEXICO",
        "estado"  : "CDMX",
        "cp"      : "06430"
    },
    {
        "tipo"    : "clievent",
        "numcli"  : "AF-1221",
        "rfc"     : "BIM011108DJ5 ",
        "nomcli"  : "BIMBO",
        "email"   : "compras@bimbo.com",
        "calle"   : "LAZARO CARDENAS",
        "numext"  : "1231",
        "colonia" : "CENTRO",
        "ciudad"  : "MEXICALI",
        "estado"  : "BC",
        "cp"      : "27001"
    }
]


```



En el POST a ClientesEventuales
usar las propiedades:
nomcliev
nombre
apellido
rfc
si manda nombre y apellido
    es persona
    rfc debe medir 13
    calcular la Posicion del separador 
si solo manda nomcliev
    si rfc=13 es Persona
    si rfc=12 es Empresa
    possep = len(allt(nomcliev))+1

## Crear Facturas

### POST  /facturador/facturas

**Encabezado - Header** 
```json 
    headers: { "X-facturadortoken" : "nota-codigofact" } 
```

request
```json
{
    "email"     : "otonieldesarrollosait@gmail.com",
	"usocfdi"   : "S01",
	"cp"        : "01160",
	"idregimen" : "616",
	"tipo"      : "cliente",  // Tipo de Cliente: cliente o clievent
	"numcli"    : "1203",
	"rfc"       : "JUFA7608212V6",
	"nombre"    : "ADRIANA JUAREZ FERNANDEZ",
}
```
Notas:
- Si numcli es vacio, entonces agregaremos un cliente eventual
- Campo tipo nos indica si el cliente enviado es cliente normal o cliente, eventual

response
```
result: {
    uuid : "5AA638B86585466FA5B9D7B90E1B6A40"
}
```
