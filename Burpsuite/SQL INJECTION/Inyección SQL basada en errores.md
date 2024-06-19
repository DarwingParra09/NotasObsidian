
La inyección SQL basada en errores se refiere a casos en los que puede utilizar mensajes de error para extraer o inferir datos confidenciales de la base de datos, incluso en contextos ciegos. Las posibilidades dependen de la configuración de la base de datos y de los tipos de errores que puedas provocar:

- Es posible que pueda inducir a la aplicación a devolver una respuesta de error específica basada en el resultado de una expresión booleana. Puedes explotar esto de la misma manera que las respuestas condicionales que vimos en la sección anterior. Para obtener más información, consulte Explotación de la inyección SQL ciega provocando errores condicionales.
- Es posible que pueda activar mensajes de error que generen los datos devueltos por la consulta. Esto efectivamente convierte las vulnerabilidades de inyección SQL que de otro modo serían ciegas en vulnerabilidades visibles. Para obtener más información, consulte Extracción de datos confidenciales mediante mensajes de error SQL detallados.

## Explotar la inyección SQL ciega provocando errores condicionales

Algunas aplicaciones realizan consultas SQL pero su comportamiento no cambia, independientemente de si la consulta devuelve algún dato. La técnica de la sección anterior no funcionará porque inyectar diferentes condiciones booleanas no afecta las respuestas de la aplicación.

A menudo es posible inducir a la aplicación a devolver una respuesta diferente dependiendo de si se produce un error de SQL. Puede modificar la consulta para que provoque un error en la base de datos solo si la condición es verdadera. Muy a menudo, un error no controlado generado por la base de datos provoca alguna diferencia en la respuesta de la aplicación, como un mensaje de error. Esto le permite inferir la verdad de la condición inyectada.

## Explotación de la inyección SQL ciega provocando errores condicionales - Continuación

Para ver cómo funciona esto, supongamos que se envían dos solicitudes que contienen `TrackingId`a su vez los siguientes valores de cookies:

```
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a 
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

Estas entradas utilizan la `CASE`palabra clave para probar una condición y devolver una expresión diferente dependiendo de si la expresión es verdadera:

- Con la primera entrada, la `CASE`expresión se evalúa como `'a'`, lo que no provoca ningún error.
- Con la segunda entrada, se evalúa como `1/0`, lo que provoca un error de división por cero.

Si el error causa una diferencia en la respuesta HTTP de la aplicación, puede usarlo para determinar si la condición inyectada es verdadera.

Con esta técnica, puedes recuperar datos probando un carácter a la vez:

`xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a`

#### Nota

Existen diferentes formas de desencadenar errores condicionales y diferentes técnicas funcionan mejor en diferentes tipos de bases de datos. Para obtener más detalles, consulte la hoja de referencia de inyección SQL.

## Caso 23:  Inyección SQL ciega con errores condicionales

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL ciega. La aplicación utiliza una cookie de seguimiento para análisis y realiza una consulta SQL que contiene el valor de la cookie enviada.

Los resultados de la consulta SQL no se devuelven y la aplicación no responde de manera diferente en función de si la consulta devuelve alguna fila. Si la consulta SQL provoca un error, la aplicación devuelve un mensaje de error personalizado.

La base de datos contiene una tabla diferente llamada `users`, con columnas llamadas `username`y `password`. Debe aprovechar la vulnerabilidad de inyección ciega de SQL para descubrir la contraseña del `administrator`usuario.

Para resolver la práctica de laboratorio, inicie sesión como `administrator`usuario.

Dentro de este laboratorio vemos que se ejecuta la base de datos Oracle, así que la petición puede verse distinto a la de otras bases de datos. 
En la siguiente petición estamos identificando la longitud que tiene la contraseña de el usuario administrador, en este tema a tratar no esperamos a que la respuesta nos de un mensaje de salida, para esta inyeccion sql ciega nosotros nos apoyamos de la caida en la peticion, el que se desborde es buena señal porque nos verifica que hay informacion de acuerdo a lo pedido

![[Pasted image 20240611224812.png]]

En la siguiente imagen nos muestra la respuesta de un error en el servidor cuando se coloca que el tamaño de la contraseña de administrador es mayor o igual a 20 caracteres 

![[Pasted image 20240611224837.png]]

Ya teniendo claro la longitud de la contraseña hacemos unos cambios y comenzamos a encontrar uno por una la posición de cada letra o número que hace parte de la contraseña del usuario administrador

![[Pasted image 20240611225211.png]]

Para agilizar y encontrar la contraseña hacemos uso del intruder de brupsuite y trabajamos con dos payload, uno para enumerar las posiciones y el otro para ir reemplazando y encontrar la letra o número correcto de la contraseña, esto puede tomar su tiempo, hay que tener en cuenta que lo que estamos buscando son codigos de estado 500.

![[Pasted image 20240611230941.png]]

Aunque el automatizar el ataque tiende a verse como mas ligero la verdad terminó siendo otra, Payload 1 confirma las posiciones correctas en donde Payload 2 indica el caracter que es parte de la contraseña 

![[Pasted image 20240611231028.png]]

Ya una vez encontrada la contraseña no dirigimos a cuenta para verificar si las credenciales obtenidas hace un momento son las correctas

![[Pasted image 20240612001012.png]]

![[Pasted image 20240612001030.png]]


## Extracción de datos confidenciales mediante mensajes de error SQL detallados

La mala configuración de la base de datos a veces genera mensajes de error detallados. Estos pueden proporcionar información que puede resultar útil para un atacante. Por ejemplo, considere el siguiente mensaje de error, que aparece después de insertar una comilla simple en un `id`parámetro:

`Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char`

Esto muestra la consulta completa que la aplicación construyó utilizando nuestra entrada. Podemos ver que en este caso, estamos inyectando una cadena entre comillas simples dentro de una `WHERE`declaración. Esto facilita la creación de una consulta válida que contenga una carga útil maliciosa. Comentar el resto de la consulta evitaría que las comillas simples superfluas rompan la sintaxis.

## Extracción de datos confidenciales mediante mensajes de error SQL detallados - Continuación

En ocasiones, es posible que pueda inducir a la aplicación a generar un mensaje de error que contenga algunos de los datos devueltos por la consulta. Esto efectivamente convierte una vulnerabilidad de inyección SQL que de otro modo sería ciega en una vulnerabilidad visible.

Puede utilizar la `CAST()`función para lograr esto. Le permite convertir un tipo de datos a otro. Por ejemplo, imagine una consulta que contenga la siguiente declaración:

`CAST((SELECT example_column FROM example_table) AS int)`

A menudo, los datos que intentas leer son una cadena. Intentar convertir esto a un tipo de datos incompatible, como `int`, puede provocar un error similar al siguiente:

`ERROR: invalid input syntax for type integer: "Example data"`

Este tipo de consulta también puede resultar útil si un límite de caracteres le impide activar respuestas condicionales.

## Caso 24: Inyección SQL basada en errores visibles

La base de datos contiene una tabla diferente llamada `users`, con columnas llamadas `username`y `password`. Para resolver la práctica de laboratorio, busque una manera de filtrar la contraseña del `administrator`usuario y luego inicie sesión en su cuenta.

La petición a continuación es una subconsulta que selecciona el nombre de usuario (`username`) de la tabla `users`, limitando el resultado a solo la primera fila. En respuesta este nos genera un error pero dentro de este nos muestra el usuario que aparece en la primera fila dentro de los datos de username

![[Pasted image 20240612102941.png]]
![[Pasted image 20240612104248.png]]

Como sabemos que el usuario en la posicion 1 es administrador, su contraseña deberia tambien serlo, asi que cambiamos username por password y la respuesta aunque nos genera un error nos da su credencial

![[Pasted image 20240612102632.png]]
![[Pasted image 20240612102858.png]]
![[Pasted image 20240612103002.png]]

