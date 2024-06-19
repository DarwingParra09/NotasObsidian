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

