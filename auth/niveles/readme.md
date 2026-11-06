## Niveles

Rutas de Niveles (grupos de usuarios)

| Accion                    | Ruta                                |
|---------------------------|-------------------------------------|
| Crear                     | POST   /api/v3/niveles              |
| [Leer](#leer-nivel)       | GET    /api/v3/niveles/:nivel       |
| Actualizar                | PUT    /api/v3/niveles/:nivel       |
| Borrar                    | DELETE /api/v3/niveles/:nivel       |
| [Buscar](#buscar-niveles) | GET    /api/v3/niveles?variables... |

Crear, Actualizar y Borrar estan registradas pero aun no estan implementadas, regresan error `no implementado`.

Campos de los Niveles

| Campo     | tipo     | Significado                                        |
|-----------|----------|----------------------------------------------------|
| id        | int      | id incremental disponible solo através de SAIT API |
| created   | datetime | timestamp del momento de creación                  |
| updated   | datetime | timestamp ultima actualizacion                     |
| nivel     | char(5)  | Identificador del nivel                            |
| desc      | varchar  | Descripcion del nivel                              |
| acceso    | varchar  | Permisos 1 a 250, `*` indica que tiene el permiso  |
| acceso2   | varchar  | Permisos 251 en adelante                           |
| creargpos | varchar  | Grupos que puede crear el nivel                    |


---
### Leer Nivel

GET /api/v3/niveles/ADMIN

response:
```json
{
  "id"        : 1,
  "created"   : "2024-10-30 17:36:09",
  "updated"   : "2024-10-30 17:36:09",
  "nivel"     : "ADMIN",
  "desc"      : "Administradores",
  "acceso"    : "***_**_***...",
  "acceso2"   : "**_*...",
  "creargpos" : ""
}
```

---
### Buscar Niveles

GET /api/v3/niveles?variables...

| Variable | Significado                                         | Uso                    |
|----------|-----------------------------------------------------|------------------------|
| q        | Palabras a buscar en nivel y desc                   | q=admin                |
| limit    | Cuantos registros obtener                           | limit=50               |
| offset   | A partir del registro a iniciar (requiere `limit`)  | offset=10              |

response:
```json
[
  {
    "id"        : "1",
    "created"   : "2024-10-30 17:36:09",
    "updated"   : "2024-10-30 17:36:09",
    "nivel"     : "ADMIN",
    "desc"      : "Administradores",
    "acceso"    : "***_**_***...",
    "acceso2"   : "**_*...",
    "creargpos" : ""
  }
]
```
