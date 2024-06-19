
En esta sección, describimos técnicas para encontrar y explotar vulnerabilidades de inyección SQL ciega.

## ¿Qué es la inyección SQL ciega?

La inyección ciega de SQL ocurre cuando una aplicación es vulnerable a la inyección de SQL, pero sus respuestas HTTP no contienen los resultados de la consulta SQL relevante ni los detalles de ningún error de la base de datos.

Muchas técnicas, como los ataques `UNION`, no son efectivas con las vulnerabilidades de inyección SQL ciega. Esto se debe a que dependen de poder ver los resultados de la consulta inyectada dentro de las respuestas de la aplicación. Todavía es posible explotar la inyección SQL ciega para acceder a datos no autorizados, pero se deben utilizar técnicas diferentes.

## Explotar la inyección SQL ciega activando respuestas condicionales

Considere una aplicación que utiliza cookies de seguimiento para recopilar análisis sobre el uso. Las solicitudes a la aplicación incluyen un encabezado de cookie como este:

`Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4`

Cuando se procesa una solicitud que contiene una cookie `TrackingId`, la aplicación utiliza una consulta SQL para determinar si se trata de un usuario conocido:

`SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'`

Esta consulta es vulnerable a la inyección SQL, pero los resultados de la consulta no se devuelven al usuario. Sin embargo, la aplicación se comporta de manera diferente dependiendo de si la consulta devuelve algún dato. Si envía un mensaje reconocido `TrackingId`, la consulta devuelve datos y recibe un mensaje de "Bienvenido" en la respuesta.

Este comportamiento es suficiente para poder explotar la vulnerabilidad de inyección SQL ciega. Puede recuperar información activando diferentes respuestas de forma condicional, dependiendo de la condición inyectada.

## Explotación de la inyección SQL ciega activando respuestas condicionales - Continuación

Para comprender cómo funciona este exploit, supongamos que se envían dos solicitudes que contienen `TrackingId`a su vez los siguientes valores de cookies:

`…xyz' AND '1'='1 …xyz' AND '1'='2`

- El primero de estos valores hace que la consulta devuelva resultados, porque la `AND '1'='1`condición inyectada es verdadera. Como resultado, se muestra el mensaje "Bienvenido de nuevo".
- El segundo valor hace que la consulta no devuelva ningún resultado porque la condición inyectada es falsa. El mensaje "Bienvenido de nuevo" no se muestra.

Esto nos permite determinar la respuesta a cualquier condición inyectada y extraer datos pieza por pieza.

## Explotación de la inyección SQL ciega activando respuestas condicionales - Continuación

Por ejemplo, supongamos que hay una tabla llamada `Users`con las columnas `Username`y `Password`y un usuario llamado `Administrator`. Puede determinar la contraseña de este usuario enviando una serie de entradas para probar la contraseña, un carácter a la vez.

Para hacer esto, comience con la siguiente entrada:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm`

Esto devuelve el mensaje "Bienvenido nuevamente", lo que indica que la condición inyectada es verdadera y, por lo tanto, el primer carácter de la contraseña es mayor que `m`.

A continuación, enviamos la siguiente entrada:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't`

Esto no devuelve el mensaje "Bienvenido", lo que indica que la condición inyectada es falsa y, por lo tanto, el primer carácter de la contraseña no es mayor que `t`.

Finalmente, enviamos la siguiente entrada, que devuelve el mensaje "Bienvenido", confirmando así que el primer carácter de la contraseña es `s`:

`xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's`

Podemos continuar este proceso para determinar sistemáticamente la contraseña completa del `Administrator`usuario.

#### Nota

La `SUBSTRING`función se llama `SUBSTR`en algunos tipos de bases de datos. Para obtener más detalles, consulte la hoja de referencia de inyección SQL.

## Caso 22: Inyección SQL ciega con respuestas condicionales

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL ciega. La aplicación utiliza una cookie de seguimiento para análisis y realiza una consulta SQL que contiene el valor de la cookie enviada.

Los resultados de la consulta SQL no se devuelven y no se muestran mensajes de error. Pero la aplicación incluye un mensaje de "Bienvenido" en la página si la consulta devuelve alguna fila.

La base de datos contiene una tabla diferente llamada `users`, con columnas llamadas `username`y `password`. Debe aprovechar la vulnerabilidad de inyección ciega de SQL para descubrir la contraseña del `administrator`usuario.

Para resolver la práctica de laboratorio, inicie sesión como `administrator`usuario.

Para esta practica de Inyección SQL ciega tenemos dentro de la cookie un identificador de acuerdo al usuario que esté trabajando en la pagina
Teniendo el `TrackingId` podemos hacer peticiones en intruder para detectar cual es el valor de cada posición de la contraseña administrador 

	Saber la longitud de posibles contraseñas
	select trackingid from trackingid table where trackingID = xyz' and 
	(select username from users where username='administrator'and LENGTH (password)>=##)='administrator'--' 

Para tener un buen manejo del tiempo pues debemos buscar de cuanto puede ser la longitud de una contraseña, una vez que es identificado podemos hacer una automatización en intruder para comenzar a armar la contraseña

![[Pasted image 20240611165214.png]]

La siguiente petición nos indica que seleccionamos la columna contraseña de usuarios donde el nombre del usuario administrador es igual a que su primer caracter dentro de su contraseña es la letra a 

`1,1 tiene un significado, el primero es la posicion del caracter, el segundo es la longitud.`

![[Pasted image 20240611170102.png]]

Por ejemplo si escribimos la petición que vemos en la imagen nos indica la primera  posición de un solo caracter.

![[Pasted image 20240611170134.png]]

Esperamos en que esta petición se nos devuelva welcome back, y con ya tenemos nuestro primero caracter dentro de la contraseña. Sino, pues manualmente o de manera automatizada comenzamos a cambiar la letra por otro o un numero hasta que nos retorne el mensaje de bienvenido.
Buscando el primero caracter la contraseña administrador vemos que el LENGTH cambia a comparación de los otro intentos y el response nos termina de confirmar que nos da como respuesta el WELCOME BACK, así que ya tenemos el inicio de la contraseña, ahora debemos seguir reemplazando la posición y seguir trabajando con la lista alfabética y numérica hasta encontrar las 20 caracteres

![[Pasted image 20240611170838.png]]

![[Pasted image 20240611213928.png]]

![[Pasted image 20240611214007.png]]

