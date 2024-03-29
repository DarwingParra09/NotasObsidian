La inyección SQL (SQLi) es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que realiza una aplicación a su base de datos. Esto puede permitir que un atacante vea datos que normalmente no puede recuperar. Esto podría incluir datos que pertenecen a otros usuarios o cualquier otro dato al que pueda acceder la aplicación. En muchos casos, un atacante puede modificar o eliminar estos datos, provocando cambios persistentes en el contenido o el comportamiento de la aplicación.

En algunas situaciones, un atacante puede escalar un ataque de inyección SQL para comprometer el servidor subyacente u otra infraestructura de back-end. También puede permitirles realizar ataques de denegación de servicio.

## Cómo detectar vulnerabilidades de inyección SQL

Puede detectar la inyección de SQL manualmente utilizando un conjunto sistemático de pruebas en cada punto de entrada de la aplicación. Para hacer esto, normalmente enviarías:

- El carácter de comilla simple `'`y busca errores u otras anomalías.
- Alguna sintaxis específica de SQL que evalúa el valor base (original) del punto de entrada y un valor diferente, y busca diferencias sistemáticas en las respuestas de la aplicación.
- Condiciones booleanas como `OR 1=1`y `OR 1=2`y busque diferencias en las respuestas de la aplicación.
- Cargas útiles diseñadas para desencadenar retrasos de tiempo cuando se ejecutan dentro de una consulta SQL y buscar diferencias en el tiempo necesario para responder.
- Cargas útiles de OAST diseñadas para desencadenar una interacción de red fuera de banda cuando se ejecuta dentro de una consulta SQL y monitorear cualquier interacción resultante.

Alternativamente, puede encontrar la mayoría de las vulnerabilidades de inyección SQL de forma rápida y confiable utilizando Burp Scanner.

## Recuperar datos ocultos

Imagine una aplicación de compras que muestra productos en diferentes categorías. Cuando el usuario hace clic en la categoría **Regalos** , su navegador solicita la URL:

`https://insecure-website.com/products?category=Gifts`

Esto hace que la aplicación realice una consulta SQL para recuperar detalles de los productos relevantes de la base de datos:

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Esta consulta SQL solicita a la base de datos que devuelva:

- Todos los detalles ( `*`)
- de la `products`mesa
- donde `category`esta`Gifts`
- y `released`es `1`.

La restricción `released = 1`se utiliza para ocultar productos que no se comercializan. Podríamos suponer que para productos inéditos, `released = 0`

## Recuperar datos ocultos - Continuación

La aplicación no implementa ninguna defensa contra ataques de inyección SQL. Esto significa que un atacante puede construir el siguiente ataque, por ejemplo:

`https://insecure-website.com/products?category=Gifts'--`

Esto da como resultado la consulta SQL:

`SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1`

Fundamentalmente, tenga en cuenta que `--`es un indicador de comentario en SQL. Esto significa que el resto de la consulta se interpreta como un comentario, eliminándolo efectivamente. En este ejemplo, esto significa que la consulta ya no incluye `AND released = 1`. Como resultado, se muestran todos los productos, incluidos aquellos que aún no se han lanzado.

Puedes utilizar un ataque similar para hacer que la aplicación muestre todos los productos en cualquier categoría, incluidas las categorías que no conocen:

`https://insecure-website.com/products?category=Gifts'+OR+1=1--`

Esto da como resultado la consulta SQL:

`SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1`

La consulta modificada devuelve todos los elementos donde es `category`o `Gifts`es `1`igual a `1`. Como `1=1`siempre ocurre, la consulta devuelve todos los elementos.

#### Advertencia

Tenga cuidado al inyectar la condición `OR 1=1`en una consulta SQL. Incluso si parece inofensivo en el contexto en el que se está inyectando, es común que las aplicaciones utilicen datos de una única solicitud en varias consultas diferentes. Si su condición llega a una declaración `UPDATE`o `DELETE`, por ejemplo, puede resultar en una pérdida accidental de datos.

# Caso 14: Vulnerabilidad de inyección SQL en la cláusula WHERE que permite la recuperación de datos ocultos

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Cuando el usuario selecciona una categoría, la aplicación realiza una consulta SQL como la siguiente:

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Para resolver la práctica de laboratorio, realice un ataque de inyección SQL que haga que la aplicación muestre uno o más productos inéditos.

Dentro de tienda virtual podemos ver las categorías que hay en base a los productos, sin importar la que escojamos la consulta SQL los dará como información en la URL como `category` 

![[Pasted image 20240329121610.png]]
![[Pasted image 20240329121635.png]]

Si hacemos la modificación y lo cambiamos por `OR+1=1--` nos dará como resultado en la petición todos los productos que en su momentos no estaban en la categoría o que no estaban a la vista del cliente

![[Pasted image 20240329121718.png]]
![[Pasted image 20240329121735.png]]

## Subvirtiendo la lógica de la aplicación

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
