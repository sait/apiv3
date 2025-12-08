## Imagenes

**agregar imagenes**

Paquete que permite subir fotos de artículos al sistema SAITNube, estas se almacenan en el bucket configurado en SAITNube.

Cada vez que se modifica un articulo en la ventana de "articulos con fotografia" en SAIT.exe, se envia el evento "ADDFOTO", 
el cual contiene el nombre de la imagen y el contenido en base64. 

Cuando el evento llega a SAITNube pueden ocurrir 3 casos:

- **Caso 1:** SAITNube detecta que la imagen no se ha agregado al sistema y procede a agregarla al bucker de SAITNube.
- **Caso 2:** SAITNube detecta que el nombre de la imagen recibida ya existe en la base de datos pero el contenido de la imagen es distinto y actualiza la imagen.
- **Caso 3:** SAITNube detecta que la imagen ya se agrego y no ha tenido cambios, por lo que procede a evitar todo el proceso y retornar.

**mostrar fotos**

Para poder observar las imagenes de los articulos contamos con la ruta "GET api/v3/articulos/:numart", que retorna toda la informacion de dicho articulo, entre ello un campo llamado "imagenes", el cual contiene un listado de las urls de cada imagen

```json
"imagenes":[
    "https://urlbase.cdn/sait-imagenes/nombreimagen1.jpg",
    "https://urlbase.cdn/sait-imagenes/nombreimagen2.jpg",
    "https://urlbase.cdn/sait-imagenes/nombreimagen3.jpg"
]
```

**Eliminar fotos**

para eliminar fotos, contamos con una gorutina que se ejecuta cada siete dias, que detecta cambos en la tabla de articulos.
si un articulo contiene un nombre menos en los campos de foto y fotos, la gorutina buscara los datos
en la tabla de imagenes y en el bucket para eliminarlos.

---
**Estructura evento ADDFOTO**

```xml
<oPlainobject>
    <aActions>
		<oPlainobject.Actions>
			<cAction>None</cAction>
			<cIndex>NUMART</cIndex>
			<oPlainobject.Info>
				<cContent>/9j/4AAQSkZBgAAD//gA7Q1JAgIDAwMD...AAwDAQAC</cContent>
				<cFilename>abrecubetas.jpg</cFilename>
			</oPlainobject.Info>
			<cKey>                ABRE</cKey>
			<oPlainobject.Keys>
				<cNumart>                ABRE</cNumart>
			</oPlainobject.Keys>
			<cTable>ARTS</cTable>
		</oPlainobject.Actions>
	</aActions>
	<nCount>1</nCount>
</oPlainobject>
```

**campos de estructura**

| campos   | tipo de dato | descripcion                                         |
|----------|--------------|-----------------------------------------------------|
| filename | string       | nombre de imagen (obligatorio enviar con extencion) |
| b64      | string       | base64 de la imagen que se subira al sistema        |

**campos de tabla Imagenes**

| campos   | tipo de dato | descripcion                                         |
|----------|--------------|-----------------------------------------------------|
| filename | string       | nombre de imagen (obligatorio enviar con extencion) |
| md5      | string       | md5 generado en base al b64 de la imagen            |
| size     | int          | base64 de la imagen que se subira al sistema        |

## Como subir fotos a saitnube y relacionarla a un articulo

### Subir una imagen

POST /api/v3/imagenes

request:
```json
{
    "filename":"nombre-de-imagen-y-extencion.jpg",
    "b64":"base63-de-imagenasc3dewr544q3efr...//4335tygrdf"
}
```

response:
```json
"OK"
```

### Relacionar imagen con un articulo

al momento de subir una o varias imagenes para un articulo, se debera realizar un PUT de dicho articulo, 
agregando el nombre de cada imagen al campo fotos, separandolos con un salto de linea

PUT /api/v3/articulos/:numart

```json
{
    "otrosCAmpos":"OtrosValores",
    "fotos":"
        imagen1.jpg
        imagen2.jpg
        imagen3.jpg
    "
}
```

### Ver imagenes de 1 articulo

GET api/v3/articulos/:numart

```json
{
    "otro-campo1":"otro-valor1",
    "otro-campo2":"otro-valor2",
    "otro-campo3":"otro-valor3",
    "otro-campo4":"otro-valor4",
    "imagenes":[
        "urlbaseSaitNubeBucketCDN/sait-imagenes/nombre-imagen1.jpg",
        "urlbaseSaitNubeBucketCDN/sait-imagenes/nombre-imagen2.jpg",
    ]
}
```