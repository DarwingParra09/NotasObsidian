En algunos casos, es posible que la consulta del ejemplo anterior solo devuelva una única columna.

Puede recuperar varios valores juntos dentro de esta única columna concatenando los valores. Puede incluir un separador que le permita distinguir los valores combinados. Por ejemplo, en Oracle podrías enviar la entrada:

`' UNION SELECT username || '~' || password FROM users--`

Esto utiliza la secuencia de doble canalización, `||`que es un operador de concatenación de cadenas en Oracle. La consulta inyectada concatena los valores de los campos `username`y `password`, separados por el `~`carácter.

Los resultados de la consulta contienen todos los nombres de usuario y contraseñas, por ejemplo:

`... administrator~s3cure wiener~peter carlos~montoya ...`

Diferentes bases de datos utilizan diferentes sintaxis para realizar la concatenación de cadenas. Para obtener más detalles, consulte la [hoja de referencia de inyección SQL](https://portswigger.net/web-security/sql-injection/cheat-sheet) .

## Caso 19: ataque UNION de inyección SQL, recuperando múltiples valores en una sola columna

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puede utilizar un ataque UNION para recuperar datos de otras tablas.

La base de datos contiene una tabla diferente llamada `users`, con columnas llamadas `username`y `password`.

Para resolver la práctica de laboratorio, realice un ataque UNION de inyección SQL que recupere todos los nombres de usuario y contraseñas, y utilice la información para iniciar sesión como `administrator`usuario.

Dentro de este laboratorio hacemos referencia a lo visto anteriormente para poder encontrar cuantas columnas se trabajar en la siguiente categoría de datos, podemos observar que tenemos 2 columnas 

![[Pasted image 20240606115453.png]]
![[Pasted image 20240606115528.png]]

Identificada las columnas buscamos cual nos puede devolver un texto 

![[Pasted image 20240606115630.png]]
![[Pasted image 20240606115650.png]]

Sabiendo donde está la columna que nos devuelve un texto buscamos concatenar los valores y que nos de como respuesta los nombres de usuario y su contraseña.

![[Pasted image 20240606115903.png]]

Obtenemos las diferentes credenciales de los usuarios y por ahora nuestro objetivo es la cuenta administrador 

![[Pasted image 20240606115922.png]]
![[Pasted image 20240606120054.png]]
