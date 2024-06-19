Imagine una aplicación que permita a los usuarios iniciar sesión con un nombre de usuario y contraseña. Si un usuario envía el nombre de usuario `wiener`y la contraseña `bluecheese`, la aplicación verifica las credenciales realizando la siguiente consulta SQL:

`SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'`

Si la consulta devuelve los detalles de un usuario, entonces el inicio de sesión se realizó correctamente. En caso contrario, se rechaza.

En este caso, un atacante puede iniciar sesión como cualquier usuario sin necesidad de contraseña. Pueden hacer esto usando la secuencia de comentarios SQL `--`para eliminar la verificación de contraseña de la `WHERE`cláusula de la consulta. Por ejemplo, enviar el nombre de usuario `administrator'--`y una contraseña en blanco da como resultado la siguiente consulta:

`SELECT * FROM users WHERE username = 'administrator'--' AND password = ''`

Esta consulta devuelve el usuario cuyo `username`nombre es `administrator`y registra exitosamente al atacante como ese usuario

# Caso 15: Vulnerabilidad de inyección SQL que permite omitir el inicio de sesión

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en la función de inicio de sesión.

Para resolver la práctica de laboratorio, realice un ataque de inyección SQL que inicie sesión en la aplicación como `administrator`usuario.

En el login sabemos de un usuario administrador pero no tenemos su contraseña, asi que vamos a vulnerar el inicio de sesion, ingresamos el usuario más un comentario que será detectado por la peticion en el sql y de contraseña entre comillas dejariamos un espacio ' ', esto nos dará una petición en el burpsuite

![[Pasted image 20240329124202.png]]
![[Pasted image 20240329124248.png]]

vemos que la sesión fue iniciada "correctamente" y tenemos acceso a la cuenta administrador de nuestra pagina, ahora si vamos a verla dentro de la interfaz de la pagina

![[Pasted image 20240329124311.png]]
![[Pasted image 20240329124420.png]]
