# UploadSait

Este paquete se encarga de recibir los datos de SaitERP y agregarlos a la base de datos de SAITNube.
Recibe los datos en un string con estructura XML desde la ventana "Subir Datos a Nube" de Sait,

 * La estructura XML tiene la caracteristica de que viene con los espacios en blanco de los DBF en lugar de eliminarlos.

Este paquete se encarga de trimear los espacios a la derecha, pero no los de la izquierda para respetar los campos index de los DBF en Sistema Sait. 

## Links
 * Doc: Como Subir DBFs a SAITNube https://ayuda.sait.mx/otros-temas/sait-sync/local-subir-tablas/
 * Programa que convierte el DBF en xml http://gitlab.sait.mx/vfp/dbf2xml/-/blob/master/dbf2xml.prg
 * Ventana en VFP para subir datos: https://sait.mx/download/sait-nube/enviarnube.zip
 * Paquete en APIv1 http://gitlab.sait.mx/saitnube/apiv1/-/tree/master/backend/migracion