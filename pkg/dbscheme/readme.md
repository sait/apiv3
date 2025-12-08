## PKG dbscheme

Este paquete se encarga de gestionar la carga y validación de información sobre los campos de una base de datos, proporcionando funcionalidades para obtener detalles específicos sobre cada campo y su estructura. Las características principales de este paquete son las siguientes:

Funciones
- [Verificar existencia de un campo](#verificar-existencia-de-un-campo)
- [Definicion de un campo](#definicion-de-un-campo)
- [Verificar existencia de una tabla](#verificar-existencia-de-una-tabla)
- [Obtener campos de una tabla](#obtener-los-campos-de-una-tabla)
- [Obtener indices de una tabla](#obtener-los-indices-de-una-tabla)
- [Validar Long de los campos](#validar-long-de-los-campos-de-una-estructura)

---
### Verificar existencia de un campo

Para verificar la existencia de un campo utilizamos la funcion campo, esta funcion ademas de retornarnos la informacion del campo tambien nos ofrece una respuesta "bool" que nos indica si el campo existe o no

En caso de utilizar la base de datos default podemos acceder directamente a la funcion campo
```go
cFullcampoName := "nombreTabla"+"."+"nombrecampo"
campo,fnd := dbscheme.campo(cFullcampoName)
if !fnd {
    return error
}
```

o bien utilizar **Get("dbname")** para otras bases de datos que no sean **default**
```go
cSchemeName := "dbname"
cFullecampoName := "nombreTabla"+"."+"nombrecampo"
campo,fnd := dbscheme.Get(cSchemeName).campo(cFullcampoName)
if !fnd {
    return error
}
```

---
### Definicion de un campo

La estructura de los campos maneja 4 datos
 - Name: nombre del campo
 - Type: tipo del campo
 - Len: longitud del campo
 - Dec: decimales del campo (solo en caso de que el tipo de dato sea decimal)

Podemos acceder a estos datos de la siguiente manera
```go
campo,fnd := dbscheme.campo(cFullcampoName)
if !fnd {
    return error
}
name:= campo.Name
tipo:= campo.Type
long:= campo.Len
dec:= campo.Dec
```

---
### Verificar existencia de una tabla

Para verificar la existencia de una tabla utilizamos la funcion Table, esta funcion ademas de retornarnos la informacion de la tabla tambien nos ofrece una respuesta "bool" que nos indica si la tabla existe o no

En caso de utilizar la base de datos **default** podemos acceder directamente a la funcion Table
```go
table,fnd := dbscheme.Table("nombreTabla")
if !fnd {
    return error
}
```

o bien utilizar **Get("dbname")** para otras bases de datos que no sean **default**
```go
cSchemeName := "dbname"
table,fnd := dbscheme.Get(cSchemeName).Table("nombreTabla")
if !fnd {
    return error
}
```

---
### Obtener los campos de una tabla

Para obtener todos los campos de una tabla tenemos podemos realizar lo siguiente
```go
cSchemeName := "dbname"
table,fnd := dbscheme.Get(cSchemeName).Table("nombreTabla")
if !fnd {
    return error
}
campos := table.campos

// Imprimir todos los campos y su informacion de la tabla
fmt.Println(campos)
```

---
### Obtener los indices de una tabla

Para obtener todos los indices de una tabla tenemos podemos realizar lo siguiente
```go
cSchemeName := "dbname"
table,fnd := dbscheme.Get(cSchemeName).Table("nombreTabla")
if !fnd {
    return error
}
indices := table.PKs

// Imprimir los nombres de las llaves primarias de la tabla
fmt.Println(indices)
```

---
### Validar long de los campos de una estructura

Para validar que las estructuras tengan campos que no excedan el maximo de caracteres permitido en los campos de la base de datos se creo la funcion:
 - ValidateLenChar(estructura any, tabla string) error

esta funcion recibe una estructura de Go y el nombre de la tabla con la que se va a comparar la estructura, cabe resaltar que los campos que valida solo son de tipo char o varchar.

retornamos error si es que un campo de la estructura excede la longitud establecida por el campo de la tabla

```go
    c := new(estructuraCliente)
	err := dbscheme.ValidateLenChar(c, "clientes")
	if err != nil {
		return err
	}
```
