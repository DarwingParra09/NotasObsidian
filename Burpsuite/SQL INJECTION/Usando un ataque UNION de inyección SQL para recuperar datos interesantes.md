
Cuando haya determinado el número de columnas devueltas por la consulta original y haya encontrado qué columnas pueden contener datos de cadena, estará en condiciones de recuperar datos interesantes.

Suponer que:

- La consulta original devuelve dos columnas, las cuales pueden contener datos de cadena.
- El punto de inyección es una cadena entre comillas dentro de la `WHERE`cláusula.
- La base de datos contiene una tabla llamada `users`con las columnas `username`y `password`.

En este ejemplo, puede recuperar el contenido de la `users`tabla enviando la entrada:

`' UNION SELECT username, password FROM users--`

Para poder realizar este ataque, necesitas saber que hay una tabla llamada `users`con dos columnas llamadas `username`y `password`. Sin esta información, tendrías que adivinar los nombres de las tablas y columnas. Todas las bases de datos modernas ofrecen formas de examinar la estructura de la base de datos y determinar qué tablas y columnas contienen

# CASO 18: ataque UNION de inyección SQL, recuperando datos de otras tablas 

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puede utilizar un ataque UNION para recuperar datos de otras tablas. Para construir un ataque de este tipo, necesita combinar algunas de las técnicas que aprendió en laboratorios anteriores.

La base de datos contiene una tabla diferente llamada `users`, con columnas llamadas `username`y `password`.

Para resolver la práctica de laboratorio, realice un ataque UNION de inyección SQL que recupere todos los nombres de usuario y contraseñas, y utilice la información para iniciar sesión como `administrator`usuario

Estando en la tienda virtual podemos ver las categorías a escoger para que nos muestre una información del producto, pero sobre la información no vamos a indagar, lo que se busca es inyectar una carga útil. 

![[Pasted image 20240502201510.png]]

![[Pasted image 20240502203031.png]]
![[Pasted image 20240502203048.png]]
![[Pasted image 20240502203348.png]]

![[Pasted image 20240502203328.png]]

