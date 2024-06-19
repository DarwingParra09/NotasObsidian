
## Vulnerabilidad de inyección SQL en la cláusula WHERE que permite la recuperación de datos ocultos

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Cuando el usuario selecciona una categoría, la aplicación realiza una consulta SQL como la siguiente:

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Para resolver la práctica de laboratorio, realice un ataque de inyección SQL que haga que la aplicación muestre uno o más productos inéditos.

Dentro de tienda virtual podemos ver las categorías que hay en base a los productos, sin importar la que escojamos la consulta SQL los dará como información en la URL como `category` 

![[Pasted image 20240329121610.png]]
![[Pasted image 20240329121635.png]]

Si hacemos la modificación y lo cambiamos por `OR+1=1--` nos dará como resultado en la petición todos los productos que en su momentos no estaban en la categoría o que no estaban a la vista del cliente

![[Pasted image 20240329121718.png]]
![[Pasted image 20240329121735.png]]


## Vulnerabilidad de inyección SQL que permite omitir el inicio de sesión

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en la función de inicio de sesión.

Para resolver la práctica de laboratorio, realice un ataque de inyección SQL que inicie sesión en la aplicación como `administrator`usuario.

En el login sabemos de un usuario administrador pero no tenemos su contraseña, asi que vamos a vulnerar el inicio de sesion, ingresamos el usuario más un comentario que será detectado por la peticion en el sql y de contraseña entre comillas dejariamos un espacio ' ', esto nos dará una petición en el burpsuite

![[Pasted image 20240329124202.png]]
![[Pasted image 20240329124248.png]]

vemos que la sesión fue iniciada "correctamente" y tenemos acceso a la cuenta administrador de nuestra pagina, ahora si vamos a verla dentro de la interfaz de la pagina

![[Pasted image 20240329124311.png]]
![[Pasted image 20240329124420.png]]
