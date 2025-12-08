## Imagenes

**campos de estructura**

| campos   | tipo de dato | descripcion                                         |
|----------|--------------|-----------------------------------------------------|
| filename | string       | nombre de imagen (obligatorio enviar con extencion) |
| b64      | string       | base64 de la imagen que se subira al sistema        |

---
**campos de tabla Imagenes**

| campos   | tipo de dato | descripcion                                         |
|----------|--------------|-----------------------------------------------------|
| filename | string       | nombre de imagen (obligatorio enviar con extencion) |
| md5      | string       | md5 generado en base al b64 de la imagen            |
| size     | int          | base64 de la imagen que se subira al sistema        |

---
### Subir una imagen

POST /api/v3/imagenes

request:
```json
{
    "filename":"nombre-de-imagen-y-extencion.jpg",
    "b64":"base64-de-imagenasc3dewr544q3efr...//4335tygrdf"
}
```

response:
```json
"OK"
```