## Config

Rutas de Configuracion

| Accion                           | Ruta                            |
|----------------------------------|---------------------------------|
| [leer](#leer-config)             | GET   /api/v3/config/:nomvar    |
| [Actualizar](#actualizar-config) | PATCH /api/v3/config/:nomvar    |
| [Listar](#listar-config)         | GET   /api/v3/config?filters... |

---
Campos de los Config

| Campo   | Significado                                      |
|---------|--------------------------------------------------|
| nomvar  | Nombre de la variable de configuracion           |
| descrip | Descripcion de la configuracion                  |
| valor   | informacion que desea almacenar en dicho campo   |
| tipo    | string informativo con el Tipo de dato del campo |

---
Configuraciones disponibles

| nomvar                          | descrip                                                                                                  | tipo     |
|---------------------------------|----------------------------------------------------------------------------------------------------------|----------|
| articulos.activo                | En listado de articulos ver solo los que esten activos                                                   | int      |
| caja.cortes.serie               | consecutivo para cortes de caja                                                                          | string   |
| empresa.alertas.emails          | Empresa: Correos a los cuales llegaran avisos y alertas del sistema Facturador                           | string   |
| empresa.cp                      |                                                                                                          | string   |
| empresa.logo.url                | URL de logo de mi empresa                                                                                | string   |
| empresa.mails.from              | Emisor del mail                                                                                          | string   |
| empresa.mails.replyto           | Receptor de respuestas al correo                                                                         | string   |
| empresa.nombre                  | Nombre de la Empresa                                                                                     | string   |
| empresa.regimenfiscal           | Regimen Fiscal de la Empresa                                                                             | string   |
| empresa.rfc                     | RFC de la Empresa                                                                                        | string   |
| facturador.cfdi.template        | template para facturas                                                                                   | longtext |
| facturador.diasmaximo           | dias permitidos para facturar despues del mes donde se realizo la compra                                 | int      |
| facturador.email.template       | Template de correo para facturador                                                                       | longtext |
| facturador.serie                | Facturador: Serie a usar para las facturas                                                               | string   |
| facturador.smtp.from            | Facturador: SMTP from                                                                                    | string   |
| facturador.smtp.host            | Facturador: SMTP Host                                                                                    | string   |
| facturador.smtp.name            | Facturador: SMTP nombre                                                                                  | string   |
| facturador.smtp.port            | Facturador: SMTP Port                                                                                    | int      |
| facturador.smtp.pswd            | Facturador: SMTP Password                                                                                | string   |
| facturador.smtp.replyto         | Facturador: Correo de soporte a quien se reenviara el mensaje                                            | string   |
| facturador.smtp.user            | Facturador: SMTP User                                                                                    | string   |
| inventario.costeo               | tipo costeo para movimientos                                                                             | string   |
| postal.apikey                   | Postal: Apikey                                                                                           | string   |
| postal.server                   | Postal: Server                                                                                           | string   |
| ventas.clientes.serie           | Serie de consecutivos para clientes                                                                      | string   |
| ventas.clievent.serie           | Ventas: Serie a usar al crear clientes eventuales                                                        | string   |
| ventas.tc                       | Tipo de cambio para convertir precios de articulos al realizar facturas, cotizaciones y notas de contado | float    |
| ventas.tccompra                 | Tipo de cambio a considerar al recibir dolares en punto de venta y en caja                               | float    |
| ventas.tccredito                | Tipo de cambio para convertir precios de articulos al realizar facturas, cotizaciones de credito         | float    |
| ventas.vend.serie               | Serie de consecutivos para vendedores                                                                    | string   |
| ventas.zonas.serie              | Serie de consecutivos para zonas                                                                         | string   |

### Leer config

GET /api/v3/config/:nomvar

```json
{
    "nomvar"  : "empresa.cp",
    "descrip" : "codigo postal de mi empresa",
    "valor"   : "26671",
    "tipo"    : "string"
}
```

---
### Actualizar Config

PUT /api/v3/config/:nomvar

request:
```json
{
    "valor":"26670"
}
```

response:
```json
"UPDATED"
```

---
### Listar Config

GET /api/v3/config?filters...

| Filters | Significado                                          |
|---------|------------------------------------------------------|
| offset  | A partir de que registro iniciar búsqueda. Default 0 |
| limit   | Cuantos registros obtener. Default 100               |
| q       | Palabras a buscar en el campo "nomvar"               |

response:
```json
[
    {
        "nomvar"  : "empresa.cp",
        "descrip" : "codigo postal de mi empresa",
        "valor"   : "26671",
        "tipo"    : "string"
    }
]
```


