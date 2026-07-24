## Usuarios

Rutas de Usuarios

| Accion                     | Ruta                                 |
|----------------------------|--------------------------------------|
| [Leer](#leer-usuario)      | GET    /api/v3/usuarios/:numzona     |
| [Buscar](#buscar-usuarios) | GET    /api/v3/usuarios?variables... |

Campos de los Usuarios

| Campo   | tipo     | Significado                                        |
|---------|----------|----------------------------------------------------|
| id      | int      | id incremental disponible solo através de SAIT API |
| created | datetime | timestamp del momento de creación                  |
| updated | datetime | timestamp ultima actualizacion                     |
| numuser | varchar  | Identificador de cliente                           |
| nomuser | varchar  | Nombre de cliente                                  |
| nivel   | varchar  | nivel de grupos y permisos de usuario              |
| email   | varchar  | email de contacto de usuario                       |

---
### Leer Usuario

GET /api/v3/usuarios/:numuser

response:
```json
{
    
}
```

---
### Buscar Usuarios

GET /api/v3/usuarios?variables

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id                |
| q        | Palabras a buscar                                    |

| Ejemplo de Búsqueda                | Ruta                       |
|------------------------------------|----------------------------|
| Limitar campos                     | /api/v3/usuarios?limit=100 |
| indicar desde que registro empezar | /api/v3/usuarios?offset=50 |

response:
```json
[
    {
    }
    {}...
]
```

