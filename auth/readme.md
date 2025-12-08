## apikeys

Rutas de apikeys

| Accion                                                              | Ruta                                    |
|---------------------------------------------------------------------|-----------------------------------------|
| [Obtener permisos](#obtener-permisos)                               | GET     /api/v3/auth/permisos           |
| [Obtener descripcion de permisos](#obtener-descripcion-de-permisos) | GET     /api/v3/auth/descpermisos       |
| [Crear](#crear-apikey)                                              | POST    /api/v3/auth/apikeys            |
| [Actualizar](#actualizar-apikey)                                    | PUT     /api/v3/auth/apikeys/:id        |
| [Eliminar](#eliminar-apikey)                                        | DELETE  /api/v3/auth/apikeys/:id        |
| [Buscar](#buscar-apikeys)                                           | GET     /api/v3/auth/apikeys?filters... |

---

Permisos de usuario

| variable                           | significado                                           |
|------------------------------------|-------------------------------------------------------|
| CatalogoDeSucursalesCambiarseDeSuc | Utilerias - Catálogo de Sucursales / Cambiarse de Suc |
| VentasCrearCotizaciones            | Ventas - Crear Cotizaciones                           |
| VentasCrearCotizacionesME          | Ventas - Crear Cotizaciones en Dolares                |
| VentasCrearFacturas                | Ventas - Crear Facturas                               |
| VentasCrearNotas                   | Ventas - Crear Notas de venta                         |
| VentasCrearPedidos                 | Ventas - Crear Facturas                               |
| VentasCrearRemisiones              | Ventas - Crear Remisiones                             |
| VentasModificarDescuentos          | Ventas - Modificar descuento al crear documentos      |
| VentasModificarPrecio              | Ventas - Modificar precio al crear documentos         |
| VentasModificarVendedor            | Ventas - Modificar Vendedor                           |
| VentasRegistrarVentas              | Ventas - Registrar Ventas                             |

### Obtener permisos

GET /api/v3/auth/permisos

response:
```json
{
    "CatalogoDeSucursalesCambiarseDeSuc": false,
    "VentasCrearCotizaciones": true,
    "VentasCrearCotizacionesME": true,
    "VentasCrearFacturas": true,
    "VentasCrearNotas": true,
    "VentasCrearPedidos": true,
    "VentasCrearRemisiones": true,
    "VentasModificarDescuentos": true,
    "VentasModificarPrecio": false,
    "VentasModificarVendedor": false,
    "VentasRegistrarVentas": true
}
```

---

### Obtener descripcion de permisos

GET /api/v3/auth/descpermisos

response:
```json
{
    "CatalogoDeSucursalesCambiarseDeSuc": "Utilerias - Catálogo de Sucursales / Cambiarse de Suc",
    "VentasCrearCotizaciones": "Ventas - Crear Cotizaciones",
    "VentasCrearCotizacionesME": "Ventas - Crear Cotizaciones en Dolares",
    "VentasCrearFacturas": "Ventas - Crear Facturas",
    "VentasCrearNotas": "Ventas - Crear Notas de venta",
    "VentasCrearPedidos": "Ventas - Crear Facturas",
    "VentasCrearRemisiones": "Ventas - Crear Remisiones",
    "VentasModificarDescuentos": "Ventas - Modificar descuento al crear documentos",
    "VentasModificarPrecio": "Ventas - Modificar precio al crear documentos",
    "VentasModificarVendedor": "Ventas - Modificar Vendedor",
    "VentasRegistrarVentas": "Ventas - Registrar Ventas"
}
```

---

Campos de apikeys

| Campo   | Significado                                        |
|---------|----------------------------------------------------|
| id      | id incremental disponible solo através de SAIT API |
| created | timestamp del momento de creación                  |
| updated | timestamp ultima actualizacion                     |
| name    | nombre de la aplicacion que usara la llave         |
| apikey  | llave de acceso a saitnube                         |

---
### Crear apikey

POST /api/v3/apikeys

request:
```json
{
    "name" : "tienda web",
}
```

response:
```json
{
    "id": 16,
    "created" : "2024-09-13 17:46:41",
    "updated" : "2024-09-13 17:46:41",
    "name" : "Tienda web2",
    "apikey" : "llsovafzravf_0iz"
}
```

---
### Actualizar apikey

PUT /api/v3/apikeys/:id

request:
```json
{
    "name" : "tienda web",
}

```

response:
```json
{
    "id": 16,
    "created" : "2024-09-13 17:46:41",
    "updated" : "2024-09-13 17:46:41",
    "name" : "Tienda web2",
    "apikey" : "llsovafzravf_0iz"
}
```

---
### Eliminar apikey

PUT /api/v3/apikeys/:id

response:
```json
"DELETED"
```



---
### Buscar apikeys

GET /api/v3/apikeys?filters...

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| q        | buscar por nombre de aplicacion                      |

response:
```json
[
    {
      "id" : 1,
      "created" : "2024-09-04 19:35:47",
      "updated" : "2024-09-04 19:35:47",
      "name" : "wc",
      "apikey" : "vsG*****"
    },
    {
      "id" : 5,
      "created" : "2024-09-12 19:22:43",
      "updated" : "2024-09-12 19:22:43",
      "name" : "app2",
      "apikey" : "nry*****"
    },
    {}...
]    
```



