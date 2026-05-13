## Plugins

# Indice de rutas
| Accion                           | Ruta                                            |
|----------------------------------|-------------------------------------------------|
| [Crear](#crear-plugin)           | POST   /api/v3/admin/plugins                    |
| [Leer](#leer-plugin)             | GET    /api/v3/admin/plugins/:nombre            |
| [Actualizar](#actualizar-plugin) | PUT    /api/v3/admin/plugins/:nombre            |
| [Borrar](#eliminar-plugin)       | DELETE /api/v3/admin/plugins/:nombre            |
| [Buscar](#listar-plugins)        | GET    /api/v3/admin/plugins?filters            |
| [Recargar](#recargar-plugin)     | POST   /api/v3/admin/plugins/:nombre/reload     |
| [Desactivar](#desactivar-plugin) | POST   /api/v3/admin/plugins/:nombre/deactivate |
| [Activar](#activar-plugin)       | POST   /api/v3/admin/plugins/:nombre/activate   |

---
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

---
### Leer plugin

```
GET /api/v1/admin/plugins/:nombre
```

Retorna el plugin con su código fuente completo.

---
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

---
### Eliminar plugin

```
DELETE /api/v1/admin/plugins/:nombre
```

---
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

Soft delete — desactiva el plugin. Su código se conserva en la DB pero deja de responder requests.

---
### Desactivar plugin

```
POST /api/v1/admin/plugins/:nombre/deactivate
```

Desactiva el plugin temporalmente. Equivalente a DELETE pero más explícito semánticamente.

---
### Activar plugin

```
POST /api/v1/admin/plugins/:nombre/activate
```

Reactiva un plugin previamente desactivado. Recarga el código desde la DB y lo pone disponible en memoria.

---
### Recargar plugin

```
POST /api/v1/admin/plugins/:nombre/reload
```

Recarga el código del plugin desde la DB sin modificarlo. Útil si el plugin fue modificado directamente en la DB o para forzar una recarga después de reiniciar la API.

- Si hay error de compilación retorna `400` y el plugin anterior **sigue activo**.

---

