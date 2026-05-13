# tengo_bridge

Bridge entre Go (Gin + ERP) y scripts Tengo. Permite extender la API del ERP con plugins escritos en [Tengo](https://github.com/d5/tengo) sin recompilar ni reiniciar el servidor.




---

## Arquitectura

```
main.go
  └── core.NewHandler(db, entidad.New)     ← rutas nativas del ERP
  └── PluginManager.LoadAll()              ← carga plugins desde DB
  └── PluginManager.RegisterRoutes(api)    ← rutas de plugins y admin
```

El bridge inyecta automáticamente en cada request un objeto `ctx` disponible en el script:

| Campo | Tipo | Descripción |
|---|---|---|
| `ctx.params` | map | Path params (`/:pk` → `ctx.params.pk`) |
| `ctx.query` | map | Query string (`?zona=Norte` → `ctx.query.zona`) |
| `ctx.body` | map | Body JSON del request |
| `ctx.headers` | map | Headers HTTP seleccionados (ver `AllowedHeaders` en `core/opts.go`) |
| `ctx.db` | objeto | Acceso directo a la DB (ver sección ctx.db) |
| `ctx.__tx` | opaco | `*sql.Tx` activa — solo POST/PUT/DELETE |
| `ctx.__db` | opaco | `*sql.DB` pool — solo GET |
| `ctx.__sess` | opaco | `*db.Sess` con usersession — para entidades especiales |

### Conversiones automáticas en `ctx.query`

| Campo | Tipo resultante |
|---|---|
| `limit`, `offset` | `int` |
| `dryrun` | `bool` |
| todo lo demás | `string` |

---

## ctx.db

Métodos disponibles en el script para operaciones directas sobre la DB:

```tengo
// SELECT — retorna {result: [...rows]} o {err: "mensaje"}
res := ctx.db.query("SELECT id, nombre FROM clientes WHERE zona = ?", zona)
if res.err != undefined {
    return {err: res.err}
}
rows := res.result

// INSERT / UPDATE / DELETE — retorna {lastInsertId, rowsAffected} o {err: "mensaje"}
res := ctx.db.exec("INSERT INTO clientes (nombre) VALUES (?)", nombre)
if res.err != undefined {
    return {err: res.err}
}
id := res.lastInsertId
```

---

## Módulos ERP disponibles

Los módulos ERP se registran en `main.go` y están disponibles para todos los plugins:

```go
tengo_bridge.RegisterERPModule("vendedor", tengo_bridge.NewERPModule(vendedor.NewPL))
tengo_bridge.RegisterERPModule("articulo", tengo_bridge.NewERPModule(articulo.NewPL))
```

Uso en el script:

```tengo
vendedor := import("vendedor")

res := vendedor.create(ctx, ctx.body)
res := vendedor.read(ctx, {numvend: ctx.params.pk})
res := vendedor.update(ctx, ctx.body)
res := vendedor.delete(ctx, {numvend: ctx.params.pk})
res := vendedor.list(ctx, undefined)
```

Todos los módulos ERP retornan `{result: ...}` o `{err: "mensaje"}`.

---

## Estructura de un plugin

Un plugin es un archivo `.tengo` almacenado en la tabla `plugins` de la DB. Contiene funciones — una por método HTTP que se desea exponer.

```tengo
fmt      := import("fmt")
vendedor := import("vendedor")  // módulo ERP opcional

// Función — el nombre es la ruta del método
addfamiliar := func(ctx) {
    nombre := ctx.body.nombre
    if nombre == undefined {
        return {err: "nombre es requerido"}
    }
    res := ctx.db.exec(
        "INSERT INTO fola_familiares (nombre) VALUES (?)", nombre
    )
    if res.err != undefined {
        return {err: res.err}
    }
    return {result: {id: res.lastInsertId, nombre: nombre}}
}

listfamiliares := func(ctx) {
    res := ctx.db.query("SELECT * FROM fola_familiares ORDER BY nombre")
    if res.err != undefined {
        return {err: res.err}
    }
    return {result: res.result}
}
```

### Contrato de retorno

Todas las funciones deben retornar uno de estos dos formatos:

```tengo
return {result: <cualquier valor>}   // éxito
return {err: "mensaje de error"}     // error de negocio
```

### Status codes automáticos

Go determina el status code según el verbo HTTP y el resultado:

| Verbo | Éxito | Error de negocio | Error de runtime |
|---|---|---|---|
| GET | 200 | 400 | 500 |
| POST | 201 | 400 | 500 |
| PUT | 200 | 400 | 500 |
| DELETE | 204 | 400 | 500 |

### Transacciones automáticas

| Verbo | Conexión | Commit/Rollback |
|---|---|---|
| GET | Pool directo (`ctx.__db`) | No aplica |
| POST / PUT / DELETE | Transacción (`ctx.__tx`) | Commit si `{result}`, Rollback si `{err}` o panic |

---

## Rutas de ejecución de plugins

```
GET    /api/v1/plugins/:plugin/:metodo
POST   /api/v1/plugins/:plugin/:metodo
PUT    /api/v1/plugins/:plugin/:metodo
DELETE /api/v1/plugins/:plugin/:metodo

GET    /api/v1/plugins/:plugin/:metodo/:pk
POST   /api/v1/plugins/:plugin/:metodo/:pk
PUT    /api/v1/plugins/:plugin/:metodo/:pk
DELETE /api/v1/plugins/:plugin/:metodo/:pk
```

Ejemplos:

```bash
POST   /api/v1/plugins/folapac-recargas/addfamiliar
GET    /api/v1/plugins/folapac-recargas/listfamiliares
GET    /api/v1/plugins/folapac-recargas/getfamiliar/FAM001
PUT    /api/v1/plugins/folapac-recargas/updfamiliar/FAM001
DELETE /api/v1/plugins/folapac-recargas/delfamiliar/FAM001
GET    /api/v1/plugins/folapac-recargas/reportemensual?mes=05&anio=2026
```

---

## Administración de plugins

Todas las rutas de administración viven bajo `/api/v1/admin/plugins`.

### Listar plugins

```
GET /api/v1/admin/plugins
```

Retorna todos los plugins activos con su metadata.

```json
{
  "result": [
    {
      "id": 1,
      "nombre": "folapac-recargas",
      "version": 3,
      "compilado_en": "2026-05-10T14:30:00Z",
      "activo": true,
      "creado_en": "2026-05-01T10:00:00Z",
      "updated_en": "2026-05-10T14:30:00Z"
    }
  ]
}
```

### Leer plugin

```
GET /api/v1/admin/plugins/:nombre
```

Retorna el plugin con su código fuente completo.

### Crear plugin

```
POST /api/v1/admin/plugins
Content-Type: application/json

{
  "nombre": "folapac-recargas",
  "codigo": "addfamiliar := func(ctx) { ... }"
}
```

- El nombre se guarda en minúsculas automáticamente.
- Valida compilación antes de guardar — si hay error retorna `400` con el detalle.
- Si ya existe un plugin con ese nombre retorna `409 Conflict`.
- El plugin queda activo y disponible inmediatamente.

### Actualizar plugin

```
PUT /api/v1/admin/plugins/:nombre
Content-Type: application/json

{
  "codigo": "addfamiliar := func(ctx) { ... versión nueva ... }"
}
```

- Valida compilación antes de guardar — si hay error retorna `400` y el plugin anterior **sigue activo**.
- Incrementa `version` en 1 y actualiza `compilado_en`.
- El nuevo código queda activo inmediatamente en memoria.

### Eliminar plugin

```
DELETE /api/v1/admin/plugins/:nombre
```

Soft delete — desactiva el plugin. Su código se conserva en la DB pero deja de responder requests.

### Desactivar plugin

```
POST /api/v1/admin/plugins/:nombre/deactivate
```

Desactiva el plugin temporalmente. Equivalente a DELETE pero más explícito semánticamente.

### Activar plugin

```
POST /api/v1/admin/plugins/:nombre/activate
```

Reactiva un plugin previamente desactivado. Recarga el código desde la DB y lo pone disponible en memoria.

### Recargar plugin

```
POST /api/v1/admin/plugins/:nombre/reload
```

Recarga el código del plugin desde la DB sin modificarlo. Útil si el plugin fue modificado directamente en la DB o para forzar una recarga después de reiniciar la API.

- Si hay error de compilación retorna `400` y el plugin anterior **sigue activo**.

---

## Tabla plugins (DB)

```sql
CREATE TABLE plugins (
    id           INT          NOT NULL AUTO_INCREMENT,
    nombre       VARCHAR(50)  NOT NULL,       -- en minúsculas, único
    codigo       TEXT         NOT NULL,       -- código fuente Tengo
    version      INT          NOT NULL DEFAULT 1,  -- incrementa en cada PUT
    compilado_en DATETIME     NULL,           -- última compilación exitosa (PUT)
    activo       TINYINT(1)   NOT NULL DEFAULT 1,
    creado_en    DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_en   DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE INDEX idx_nombre (nombre),
    INDEX idx_activo (activo)
);
```

---

## Archivos del paquete

| Archivo | Descripción |
|---|---|
| `bridge.go` | `buildTengoContext` — construye el ctx del script |
| `db_object.go` | `ctx.db.query()` y `ctx.db.exec()` |
| `erp_module.go` | `NewERPModule[T]` — módulo genérico para entidades ERP |
| `gin_module.go` | `AddRoute`, `ActivateRoutes`, `RegisterERPModule` |
| `plugin_manager.go` | `PluginManager` — gestión y ejecución de plugins |
| `plugin_store.go` | CRUD de plugins en la DB |
| `router_object.go` | `*gin.RouterGroup` como objeto opaco para Tengo |
| `sess_object.go` | `*db.Sess` como objeto opaco para entidades especiales |
| `tx_object.go` | `TxObject` y `DBPoolObject` — conexiones opacas |
| `zona_module.go` | Ejemplo de módulo para entidad especial con `db.Sess` |
