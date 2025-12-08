# Eventos

## Paquete para manejo de Eventos 
SAIT Distribuido usa el evento version 1.2
Los Hooks a otras apps, usan el evento version 2


### Por hacer
- Llevar archivos de logs: 
- aaaamm.syncsd.txt
    - registro de eventos procesados ok
- aaaamm.syncsd.err.txt
    - muestra las fallas
    - Evento con error solo logearlo una vez
- syncsd.status.txt
    - almacena el status actual: { aaammdd hhmmss ulteventoid, OK/ERR, descripcion-de-falla, eventoxml-con-falla }
- En caso de falla, avanzar hasta el evento anterior
- Definir como manejar los campos adicionales de arts,clientes,provedor,docum, pq no podemos incluirlos en la structura
- Almacenar credenciales de la cuenta de sd 
- Almacenar el ultimo evento procesado
- Hacer uso de la api de sd para acceder a eventos y reportar fallas


## Introducción

**syncsd** realiza la sincronizacion entre los eventosV1 de SAITDistribuido almacenados en sd.sait.mx y la SAITAPI.

Se trata de un ciclo infinito que va leyendo eventos en bloques y procesandolos en la base de datos de SAITAPI.

Llamo "procesar" un evento a la accion de reflejarlo en las tablas de SAITAPI.

Para procesar un evento fue necesario:
- Representar el eventoV1 en estructuras de Go
- Cargar el evento en la estructura
- Recorrer las Acciones
- Crear un mapa que contenga los nombres y valores de los campos de la accion
- Formato Mapa creado es: mapa[nombreCampo] = valorCampo


### Control de errores

En caso de presentarse una falla al procesar una accion de un eventos, se genera un panic y es capturado en **procesarEventos()** haciendo RollBack a lo que se llevaba de la transaccion.

Ademas en **loopEventos()** al detectar una falla esperaremos 10 segundos para volver a iniciar el procesamiento de eventos.


### Formato EventoV1

Recordemos el formato del Evento Versión 1

```xml
<oPlainobject>
	<aActions>
		<oPlainobject.Actions>
			<cAction>AddReg</cAction>
			<oPlainobject.Fields>
				<nCostopro>859.56000</nCostopro>
				<nExistencia>1.000</nExistencia>
				<cNumalm> 1</cNumalm>
				<cNumart>       SYD-1508004IT</cNumart>
				<nVentanoqty>166.00</nVentanoqty>
			</oPlainobject.Fields>
			<cIndex>ARTALM</cIndex>
			<cKey>       SYD-1508004IT 1</cKey>
			<cTable>MULTIALM</cTable>
		</oPlainobject.Actions>
		<oPlainobject.Actions>
			<cAction>AddReg</cAction>
			<oPlainobject.Fields>
				<nCostopro>859.56000</nCostopro>
				<nExistencia>1.000</nExistencia>
				<cNumalm> 1</cNumalm>
				<cNumart>       SYD-1508003IT</cNumart>
				<nVentanoqty>160.00</nVentanoqty>
			</oPlainobject.Fields>
			<cIndex>ARTALM</cIndex>
			<cKey>       SYD-1508003IT 1</cKey>
			<cTable>MULTIALM</cTable>
		</oPlainobject.Actions>
	</aActions>
	<nCount>2</nCount>
</oPlainobject>
```

Vemos que los eventosV1:
- Se componen de varias acciones: AddReg, ActReg, DelReg
- Los nombres de los nodos contiene un prefijo que denota el tipo de campo en DBF
- Los tipos de campo en DBF son:
	- C Caracter
	- N Numerico
	- D Fecha
	- T Fechahora
	- L Boleano


### Evento sin campos llave

Hay algunos eventos que no incluyen  los campos de la llave primaria, por ejemplo:

```xml
<oPlainobject>
	<aActions>
		<oPlainobject.Actions>
			<cAction>ActReg</cAction>
			<oPlainobject.Fields>
				<cAplicacion>92-18 Nissan Tsuru III y GS</cAplicacion>
				<dFecha>20190621</dFecha>
			</oPlainobject.Fields>
			<cIndex>NUMART</cIndex>
			<cKey>           GRC-55657</cKey>
			<cTable>ARTS</cTable>
		</oPlainobject.Actions>
	</aActions>
	<nCount>1</nCount>
</oPlainobject>
```
Este evento se usa para acualizar los datos adicionales de la empresa.

O por ejemplo
```xml
		<oPlainobject.Actions>
			<cAction>ActReg</cAction>
			<oPlainobject.Fields>
				<cNumfact>   M412464</cNumfact>
				<nStatus>5</nStatus>
			</oPlainobject.Fields>
			<cIndex>TIPONUM</cIndex>
			<cKey> N   M319863</cKey>
			<cTable>DOCUM</cTable>
		</oPlainobject.Actions>
```

Esta accion se utiliza en el evento de AddFact cuando se trata de la factura global


Para procesar estas acciones en VFP, se usa el nodo  ```<cKey>``` que es un string que contiene el valor de la llave primaria de la tabla.

Esto dificulta el procesamieto de la accion porque debemos descomponer esa cadena cKey  en los correspondientes campos ver **crearKeyValues()**

En la accion de arriba el nodo ```<cKey> N   M319863</cKey>``` agrega al mapa de campos los elementos:
```
mapa["tipodoc"] = " N"
mapa["numdoc"] = "   M319863"
```

Para lo cual es necesario contar con una estructura que nos permita identificar para cada tabla:
- Campos de llave primaria
- Longitud de campo 
De esa forma descomponer el string de la llave primaria en el mapa resultante


### Sugerencia para EventoV1.2

Para evitar tener esa estructura y por la dificultad de manejar las tablas de modulos especiales sugiero agregar al eventoV1 los nodos: 
- cPkFlds que indican los nombres de los campos que forma la llave primaria (PK)
- cPkLens que indica la longitud de los campos de la PK
quedando la accion asi:

```xml
<oPlainobject.Actions>
	<cAction>ActReg</cAction>
	<oPlainobject.Fields>
		<cNumfact>   M412464</cNumfact>
		<nStatus>5</nStatus>
	</oPlainobject.Fields>
	<cIndex>TIPONUM</cIndex>
	<cKey> N   M319863</cKey>
	<cPkflds>TIPODOC+NUMDOC</cPkflds>
	<cPklens>2+10</cPklens>
	<cTable>DOCUM</cTable>
</oPlainobject.Actions>

```
  
