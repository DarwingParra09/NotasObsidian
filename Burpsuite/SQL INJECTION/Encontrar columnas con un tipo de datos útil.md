
Un ataque UNION de inyección SQL le permite recuperar los resultados de una consulta inyectada. Los datos interesantes que desea recuperar normalmente están en forma de cadena. Esto significa que necesita encontrar una o más columnas en los resultados de la consulta original cuyo tipo de datos sea, o sea compatible con, datos de cadena.

Después de determinar la cantidad de columnas requeridas, puede sondear cada columna para probar si puede contener datos de cadena. Puede enviar una serie de `UNION SELECT`cargas útiles que coloquen un valor de cadena en cada columna por turno. Por ejemplo, si la consulta devuelve cuatro columnas, deberá enviar:

`' UNION SELECT 'a',NULL,NULL,NULL-- ' UNION SELECT NULL,'a',NULL,NULL-- ' UNION SELECT NULL,NULL,'a',NULL-- ' UNION SELECT NULL,NULL,NULL,'a'--`

Si el tipo de datos de la columna no es compatible con los datos de cadena, la consulta inyectada provocará un error en la base de datos, como por ejemplo:

`Conversion failed when converting the varchar value 'a' to data type int.`

Si no se produce un error y la respuesta de la aplicación contiene algún contenido adicional, incluido el valor de cadena inyectado, entonces la columna relevante es adecuada para recuperar datos de cadena.

# CASO 17: ataque UNION de inyección SQL, búsqueda de una columna que contiene texto

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puede utilizar un ataque UNION para recuperar datos de otras tablas. Para construir un ataque de este tipo, primero debe determinar la cantidad de columnas devueltas por la consulta. Puede hacer esto usando una técnica que aprendió en una práctica de laboratorio anterior. El siguiente paso es identificar una columna que sea compatible con datos de cadena.

El laboratorio proporcionará un valor aleatorio que deberá hacer aparecer en los resultados de la consulta. Para resolver la práctica de laboratorio, realice un ataque UNION de inyección SQL que devuelva una fila adicional que contenga el valor proporcionado. Esta técnica le ayuda a determinar qué columnas son compatibles con datos de cadena.

Para este reto lo que estamos buscando el detectar que columnas trabajan con el tipo de dato string (cadena)

![[Pasted image 20240414005607.png]]

Tenemos de nuevo nuestra tienda online, usando la información del tema anterior se puede consultar cuantas columnas hay
Para esto pues se escoge una categoría y con ayuda del repetidor hacemos un cambio en la petición hasta que nos de un código de estado 200

![[Pasted image 20240414010807.png]]
![[Pasted image 20240414011258.png]]

Se puede ver que estamos tratando con 3 columnas, ya con esto identificado tenemos que buscar cual es la columna que maneja la cadena de string, para eso debemos ir intercambiando los null por la cadena que nos da el reto, en esta ocasión es: vtjEy8

![[Pasted image 20240414011815.png]]
![[Pasted image 20240414011833.png]]
La respuesta nos dio un código de estado 500, quiere decir que la primera columna no es la que contiene los datos string, seguimos intentando en el siguiente NULL

![[Pasted image 20240414012122.png]]
![[Pasted image 20240414012138.png]]
Parece ser que la columna que contiene los datos string es la columna 2, nos devuelve un código de estado 200 que nos confirma que la petición fue exitosa, ahora si hacemos los cambios en la pagina veriamos lo siguiente 

![[Pasted image 20240414012357.png]]
