## SatProd

Rutas de Artículos

| Acción            | Ruta                         |
|-------------------|------------------------------|
| [Leer](#leer)     | GET  /api/v3/satprod/:clave  |
| [Listar](#listar) | GET  /api/v3/satprod?filters |

---
### Leer

GET /api/v3/satprod/:clave

response:
```json
{
  "id": 8174,
  "created": "2025-08-04 17:27:50",
  "updated": "2025-08-04 17:27:50",
  "clave": "11162114",
  "nombre": "Telas o cintas de sujeción por contacto",
  "matpeligro": "0,1"
}
```

---
### Listar 

GET /api/v3/satprod?filters...

| Variable | Significado                                          |
|----------|------------------------------------------------------|
| offset   | A partir de que registro iniciar búsqueda. Default 0 |
| limit    | Cuantos registros obtener. Default 100               |
| order    | Orden deseado. Disponibles:updated,id,numart         |
| q        | Palabras a buscar: nombre                            |

response:
```json
[
  {
    "id": 8009,
    "created": "2025-08-04 17:27:50",
    "updated": "2025-08-04 17:27:50",
    "clave": "11121600",
    "nombre": "Madera",
    "matpeligro": "0"
  },
  {
    "id": 8162,
    "created": "2025-08-04 17:27:50",
    "updated": "2025-08-04 17:27:50",
    "clave": "11162100",
    "nombre": "Tejidos o telas especiales",
    "matpeligro": "0,1"
  },
  {}...
]
```
