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
    "CajaAbrirCorte": false,
    "CajaAutoCiertosTiposMovimiento": false,
    "CajaCerrarCorte": false,
    "CajaConsultarCortes": false,
    "CajaConsultarMovimientos": false,
    "CajaRecibirPagos": false,
    "CajaRegistrarVentas": false,
    "CatalogoDeSucursalesCambiarseDeSuc": false,
    "InventarioAccesoCatArticulos": false,
    "InventarioActualizarPreciosArt": false,
    "InventarioCategoArticulos": false,
    "InventarioClasifArticulos": false,
    "InventarioDeptosArticulos": false,
    "InventarioEliminarArticulos": false,
    "InventarioFamiliasArticulos": false,
    "InventarioGrabarArticulo": false,
    "InventarioLineasArticulos": false,
    "UsuarioSAITNube": false,
    "UtileriasConfigDelSistema": false,
    "VentasAgregarClientes": false,
    "VentasCatalogoVendedores": false,
    "VentasCrearCotizaciones": false,
    "VentasCrearCotizacionesME": false,
    "VentasCrearCotizacionesMN": false,
    "VentasCrearFacturas": false,
    "VentasCrearNotas": false,
    "VentasCrearPedidos": false,
    "VentasCrearPedidosCred": false,
    "VentasCrearPedidosME": false,
    "VentasCrearPedidosMN": false,
    "VentasCrearRemisiones": false,
    "VentasEliminarClientes": false,
    "VentasEliminarProveedores": false,
    "VentasModDatosCreditoCliente": false,
    "VentasModRfcNombreCliente": false,
    "VentasModificarCotizacionesPedidos": false,
    "VentasModificarDescuentos": false,
    "VentasModificarPrecio": false,
    "VentasModificarVendedor": false,
    "VentasRegistrarVentas": false,
    "VentasVerCatalogoProveedores": false,
    "VentasZonasYClasifDeClientes": false
}
```

---

### Obtener descripcion de permisos

GET /api/v3/auth/descpermisos

response:
```json
{
    "CajaAbrirCorte": "Caja - Permiso para abrir corte",
    "CajaAutoCiertosTiposMovimiento": "Caja - Autorizar uso de ciertos tipos de movimiento",
    "CajaCerrarCorte": "Caja - Permiso para cerrar corte",
    "CajaConsultarCortes": "Caja - Consultar cortes de caja",
    "CajaConsultarMovimientos": "Caja - Consultar movimientos de caja",
    "CajaRecibirPagos": "Caja - Permiso para Recibir pagos",
    "CajaRegistrarVentas": "Caja - Permiso para Registrar ventas",
    "CatalogoDeSucursalesCambiarseDeSuc": "Utilerias - Catálogo de Sucursales / Cambiarse de Suc",
    "InventarioAccesoCatArticulos": "Acceso al catalogo de articulos",
    "InventarioActualizarPreciosArt": "Actualizar Precios de articulos",
    "InventarioCategoArticulos": "Categorias de articulos",
    "InventarioClasifArticulos": "Clasificacion de articulos",
    "InventarioDeptosArticulos": "Departamentos de articulos",
    "InventarioEliminarArticulos": "Eliminar articulos",
    "InventarioFamiliasArticulos": "Familias de articulos",
    "InventarioGrabarArticulo": "Grabar articulos",
    "InventarioLineasArticulos": "Lineas de articulos",
    "UsuarioSAITNube": "General - Usuario SAIT Nube",
    "UtileriasConfigDelSistema": "Utilerias - Configuración del Sistema",
    "VentasAgregarClientes": "Ventas - Agregar nuevos clientes",
    "VentasCatalogoVendedores": "Ventas - Acceder a catalogo de vendedores",
    "VentasCrearCotizaciones": "Ventas - Crear Cotizaciones",
    "VentasCrearCotizacionesME": "Ventas - Crear Cotizaciones en Dolares",
    "VentasCrearCotizacionesMN": "Ventas - Crear Cotizaciones en Pesos",
    "VentasCrearFacturas": "Ventas - Crear Facturas",
    "VentasCrearNotas": "Ventas - Crear Notas de venta",
    "VentasCrearPedidos": "Ventas - Crear Facturas",
    "VentasCrearPedidosCred": "Ventas - Crear pedidos a Credito",
    "VentasCrearPedidosME": "Ventas - Crear pedidos en Dolares",
    "VentasCrearPedidosMN": "Ventas - Crear pedidos en Pesos",
    "VentasCrearRemisiones": "Ventas - Crear Remisiones",
    "VentasEliminarClientes": "Ventas - Eliminar clientes",
    "VentasEliminarProveedores": "Ventas - Eliminar proveedores",
    "VentasModDatosCreditoCliente": "Ventas - Modificar datos de crédito (en catalogo de clientes)",
    "VentasModRfcNombreCliente": "Ventas - Permitir modificar RFC o nombre (en Catalogo de Clientes)",
    "VentasModificarCotizacionesPedidos": "ventas - Modificar cotizaciones y pedidos",
    "VentasModificarDescuentos": "Ventas - Modificar descuento al crear documentos",
    "VentasModificarPrecio": "Ventas - Modificar precio al crear documentos",
    "VentasModificarVendedor": "Ventas - Modificar Vendedor",
    "VentasRegistrarVentas": "Ventas - Registrar Ventas",
    "VentasVerCatalogoProveedores": "Ventas - Catalogo de proveedores",
    "VentasZonasYClasifDeClientes": "Ventas - Definir zonas y clasificaciones (de Clientes)"
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
    "name" : "tienda web",
    "apikey" : "llsovafzravf_0iz"
}
```

---
### Actualizar apikey

PUT /api/v3/apikeys/:id

request:
```json
{
    "name" : "Tienda web2",
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

GET /api/v3/auth/apikeys?filters...

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



