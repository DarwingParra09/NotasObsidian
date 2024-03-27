La inyección de comandos del sistema operativo también se conoce como inyección de shell. Permite a un atacante ejecutar comandos del sistema operativo (SO) en el servidor que ejecuta una aplicación y, por lo general, compromete completamente la aplicación y sus datos. A menudo, un atacante puede aprovechar una vulnerabilidad de inyección de comandos del sistema operativo para comprometer otras partes de la infraestructura de alojamiento y explotar las relaciones de confianza para dirigir el ataque a otros sistemas dentro de la organización.

## Comandos útiles

Después de identificar una vulnerabilidad de inyección de comandos del sistema operativo, es útil ejecutar algunos comandos iniciales para obtener información sobre el sistema. A continuación se muestra un resumen de algunos comandos que son útiles en las plataformas Linux y Windows:

|Propósito del mando|linux|ventanas|
|---|---|---|
|Nombre del usuario actual|`whoami`|`whoami`|
|Sistema operativo|`uname -a`|`ver`|
|Configuración de la red|`ifconfig`|`ipconfig /all`|
|Conexiones de red|`netstat -an`|`netstat -an`|
|Procesos corriendo|`ps -ef`|`tasklist`|
## Inyectar comandos del sistema operativo

En este ejemplo, una aplicación de compras permite al usuario ver si un artículo está disponible en una tienda en particular. Se accede a esta información a través de una URL:

`https://insecure-website.com/stockStatus?productID=381&storeID=29`

Para proporcionar información bursátil, la aplicación debe consultar varios sistemas heredados. Por razones históricas, la funcionalidad se implementa llamando a un comando de shell con los ID del producto y de la tienda como argumentos:

`stockreport.pl 381 29`

Este comando genera el estado del stock del artículo especificado, que se devuelve al usuario.

La aplicación no implementa defensas contra la inyección de comandos del sistema operativo, por lo que un atacante puede enviar la siguiente entrada para ejecutar un comando arbitrario:

`& echo aiwefwlguh &`

Si esta entrada se envía en el `productID`parámetro, el comando ejecutado por la aplicación es:

`stockreport.pl & echo aiwefwlguh & 29`

El `echo`comando hace que la cadena proporcionada se repita en la salida. Esta es una forma útil de probar algunos tipos de inyección de comandos del sistema operativo. El `&`carácter es un separador de comandos de shell. En este ejemplo, hace que se ejecuten tres comandos separados, uno tras otro. La salida devuelta al usuario es:

`Error - productID was not provided aiwefwlguh 29: command not found`

Las tres líneas de resultados demuestran que:

- El comando original `stockreport.pl`se ejecutó sin los argumentos esperados y, por lo tanto, devolvió un mensaje de error.
- El comando inyectado `echo`se ejecutó y la cadena proporcionada se repitió en la salida.
- El argumento original `29`se ejecutó como un comando, lo que provocó un error.

Colocar el separador de comando adicional `&`después del comando inyectado es útil porque separa el comando inyectado de lo que sigue al punto de inyección. Esto reduce la posibilidad de que lo siguiente impida que se ejecute el comando inyectado.

# Caso 13 :Inyección de comandos del sistema operativo, caso simple

Esta práctica de laboratorio contiene una vulnerabilidad de inyección de comandos del sistema operativo en el verificador de existencias de productos.

La aplicación ejecuta un comando de shell que contiene los ID de tienda y producto proporcionados por el usuario, y devuelve el resultado sin procesar del comando en su respuesta.

Para resolver la práctica de laboratorio, ejecute el `whoami`comando para determinar el nombre del usuario actual.

Entramos en cualquier de los productos y vemos si hay todavía hay existencias o productos, desde este momento el burpsuite debe estar activado y registrar la petición 

![[Pasted image 20240327163527.png]]

Con la petición solicitada ahora es vista por el programa y podemos ver el identificador del producto, tomamos la petición y la enviamos al intruder para hacer unos cambios para probar accesos

![[Pasted image 20240327163511.png]]

Hacemos la siguiente modificación para con ello podamos observar el usuario actual con el que estamos trabajando dentro de nuestra pagina

![[Pasted image 20240327163655.png]]
![[Pasted image 20240327163711.png]]


