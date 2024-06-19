Cuando realiza un ataque UNION de inyección SQL, existen dos métodos efectivos para determinar cuántas columnas se devuelven de la consulta original.

Un método implica inyectar una serie de `ORDER BY`cláusulas e incrementar el índice de la columna especificada hasta que ocurra un error. Por ejemplo, si el punto de inyección es una cadena entre comillas dentro de la `WHERE`cláusula de la consulta original, deberá enviar:

`' ORDER BY 1-- ' ORDER BY 2-- ' ORDER BY 3-- etc.`

Esta serie de cargas útiles modifica la consulta original para ordenar los resultados por diferentes columnas en el conjunto de resultados. La columna de una `ORDER BY`cláusula se puede especificar por su índice, por lo que no es necesario conocer los nombres de ninguna columna. Cuando el índice de columna especificado excede el número de columnas reales en el conjunto de resultados, la base de datos devuelve un error, como por ejemplo:

`The ORDER BY position number 3 is out of range of the number of items in the select list.`

En realidad, la aplicación podría devolver el error de la base de datos en su respuesta HTTP, pero también puede emitir una respuesta de error genérica. En otros casos, es posible que simplemente no arroje ningún resultado. De cualquier manera, siempre que pueda detectar alguna diferencia en la respuesta, puede inferir cuántas columnas se devuelven de la consulta.

El segundo método implica enviar una serie de `UNION SELECT`cargas útiles que especifican un número diferente de valores nulos:

`' UNION SELECT NULL-- ' UNION SELECT NULL,NULL-- ' UNION SELECT NULL,NULL,NULL-- etc.`

Si el número de valores nulos no coincide con el número de columnas, la base de datos devuelve un error, como por ejemplo:

`All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.`

Usamos `NULL`como valores devueltos por la `SELECT`consulta inyectada porque los tipos de datos en cada columna deben ser compatibles entre las consultas originales y las inyectadas. `NULL`es convertible a todos los tipos de datos comunes, por lo que maximiza la posibilidad de que la carga útil tenga éxito cuando el recuento de columnas sea correcto.

Al igual que con la `ORDER BY`técnica, la aplicación puede devolver el error de la base de datos en su respuesta HTTP, pero puede devolver un error genérico o simplemente no devolver ningún resultado. Cuando el número de valores nulos coincide con el número de columnas, la base de datos devuelve una fila adicional en el conjunto de resultados, que contiene valores nulos en cada columna. El efecto sobre la respuesta HTTP depende del código de la aplicación. Si tiene suerte, verá contenido adicional dentro de la respuesta, como una fila adicional en una tabla HTML. De lo contrario, los valores nulos podrían desencadenar un error diferente, como un archivo `NullPointerException`. En el peor de los casos, la respuesta podría tener el mismo aspecto que una respuesta provocada por un número incorrecto de valores nulos. Esto haría que este método fuera ineficaz.

# Caso 16: Ataque UNION de inyección SQL, determinando el número de columnas devueltas por la consulta

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puede utilizar un ataque UNION para recuperar datos de otras tablas. El primer paso de un ataque de este tipo es determinar el número de columnas que devuelve la consulta. Luego utilizará esta técnica en laboratorios posteriores para construir el ataque completo.

Para resolver la práctica de laboratorio, determine la cantidad de columnas devueltas por la consulta realizando un ataque UNION de inyección SQL que devuelva una fila adicional que contiene valores nulos.

Para este caso vemos que  tenemos una tienda online con sus categorías, el objetivo de este reto es saber acerca de cuantas columnas hay en uso dentro de la pagina utilizando una ataque UNION de inyeccion SQL   

![[Pasted image 20240330121919.png]]

Cuando se elige alguna categoría nos envía a la ubicación y con ellos no da una información de los productos que están disponibles, pero esto no es lo que estamos buscando, nuestro objetivo es la URL de donde se van a filtrar los productos, comenzamos editando '+UNION+SELECT+NULL-- para con ella saber si el número de columnas devueltas es igual a 1, en dentro de la respuesta nos enviaba un código de estado 500, comenzamos a concatenar más NULL hasta que nuestro código de estado es 200 

![[Pasted image 20240330122728.png]]
![[Pasted image 20240330122749.png]]

Con este confirmamos que el tamaño de las columnas es 3 

![[Pasted image 20240330122607.png]]

## Sintaxis específica de la base de datos

En Oracle, cada `SELECT`consulta debe utilizar la `FROM`palabra clave y especificar una tabla válida. Hay una tabla integrada en Oracle llamada `dual`que se puede utilizar para este propósito. Entonces, las consultas inyectadas en Oracle deberían verse así:

`' UNION SELECT NULL FROM DUAL--`

Las cargas útiles descritas utilizan la secuencia de comentarios de doble guión `--`para comentar el resto de la consulta original después del punto de inyección. En MySQL, la secuencia de doble guión debe ir seguida de un espacio. Alternativamente, `#`se puede utilizar el carácter almohadilla para identificar un comentario.

Para obtener más detalles sobre la sintaxis específica de la base de datos, consulte la [hoja de referencia de inyección SQL](https://portswigger.net/web-security/sql-injection/cheat-sheet)