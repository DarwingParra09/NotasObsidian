
Las vulnerabilidades de carga de archivos ocurren cuando un servidor web permite a los usuarios cargar archivos en su sistema de archivos sin validar suficientemente elementos como su nombre, tipo, contenido o tamaño. No aplicar adecuadamente las restricciones sobre estos podría significar que incluso una función básica de carga de imágenes pueda usarse para cargar archivos arbitrarios y potencialmente peligrosos. Esto podría incluso incluir archivos de script del lado del servidor que permitan la ejecución remota de código.

En algunos casos, el hecho de cargar el archivo es por sí solo suficiente para causar daños. Otros ataques pueden implicar una solicitud HTTP de seguimiento del archivo, normalmente para activar su ejecución por parte del servidor.

## ¿Cómo surgen las vulnerabilidades en la carga de archivos?

Dados los peligros bastante obvios, es raro que los sitios web no tengan restricciones de ningún tipo sobre los archivos que los usuarios pueden cargar. Más comúnmente, los desarrolladores implementan lo que creen que es una validación sólida que es inherentemente defectuosa o puede ser fácilmente eludida.

Por ejemplo, pueden intentar incluir en la lista negra tipos de archivos peligrosos, pero no tienen en cuenta las discrepancias en el análisis al verificar las extensiones de los archivos. Al igual que con cualquier lista negra, también es fácil omitir accidentalmente tipos de archivos más oscuros que aún pueden ser peligrosos.

## Explotar la carga de archivos sin restricciones para implementar un shell web

Desde una perspectiva de seguridad, el peor escenario posible es cuando un sitio web le permite cargar scripts del lado del servidor, como archivos PHP, Java o Python, y también está configurado para ejecutarlos como código. Esto hace que sea trivial crear su propio shell web en el servidor.

```
Shell web

Un web shell es un script malicioso que permite a un atacante ejecutar comandos arbitrarios en un servidor web remoto simplemente enviando solicitudes HTTP al punto final correcto.
```

Si puede cargar con éxito un shell web, efectivamente tendrá control total sobre el servidor. Esto significa que puede leer y escribir archivos arbitrarios, filtrar datos confidenciales e incluso usar el servidor para realizar ataques tanto contra la infraestructura interna como contra otros servidores fuera de la red. Por ejemplo, el siguiente resumen PHP podría usarse para leer archivos arbitrarios del sistema de archivos del servidor:

`<?php echo file_get_contents('/path/to/target/file'); ?>`

Una vez cargado, enviar una solicitud para este archivo malicioso devolverá el contenido del archivo de destino en la respuesta.

Un shell web más versátil podría verse así:

`<?php echo system($_GET['command']); ?>`

Este script le permite pasar un comando del sistema arbitrario a través de un parámetro de consulta de la siguiente manera:

`GET /example/exploit.php?command=id HTTP/1.1`

# Caso 11: Ejecución remota de código mediante carga de shell web

Esta práctica de laboratorio contiene una función de carga de imágenes vulnerables. No realiza ninguna validación de los archivos que los usuarios cargan antes de almacenarlos en el sistema de archivos del servidor.

Para resolver la práctica de laboratorio, cargue un shell web PHP básico y utilícelo para filtrar el contenido del archivo `/home/carlos/secret`. Envíe este secreto usando el botón provisto en el banner del laboratorio.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

Como ya sabemos tenemos las credenciales de un usuario, estas son digitadas y nos llevarán a la cuenta aquí encontraremos una opción para subir lo que por sentido común seria una imagen, pero dado a que esto es una prueba de vulnerabilidades de carga, voy a cagar un comando en PHP y obtener el mensaje secreto

![[Pasted image 20240324003953.png]]

Lo que hace este código es darnos la petición de la ruta /home/carlos/secret, que es donde se aloja la solución del reto

![[Pasted image 20240324005540.png]]

Una vez enviada vemos que el icono del avatar no muestra imagen, pero sabemos que si está cargado el archivo, el paso a seguir y ver en donde se aloja la "imagen" asi que nos dirigimos al codigo fuente, ya se por el navegador como por burpsuite

![[Pasted image 20240324005642.png]]

Ya identificado la ruta en donde se alojan las imágenes nos dirigimos a ella y directamente se ejecuta el script y nos devuelve en mensaje secreto

![[Pasted image 20240324005707.png]]
![[Pasted image 20240324005725.png]]

## Explotación de la validación defectuosa de la carga de archivos

En la naturaleza, es poco probable que encuentre un sitio web que no tenga protección contra ataques de carga de archivos como vimos en la práctica de laboratorio anterior. Pero el hecho de que existan defensas no significa que sean sólidas. A veces aún es posible explotar las fallas en estos mecanismos para obtener un shell web para la ejecución remota de código.

## Validación de tipo de archivo defectuosa

Al enviar formularios HTML, el navegador normalmente envía los datos proporcionados en una `POST`solicitud con el tipo de contenido `application/x-www-form-url-encoded`. Esto está bien para enviar texto simple como su nombre o dirección. Sin embargo, no es adecuado para enviar grandes cantidades de datos binarios, como un archivo de imagen completo o un documento PDF. En este caso, se prefiere el tipo de contenido `multipart/form-data`

## Validación de tipo de archivo defectuosa - Continuación

Considere un formulario que contenga campos para cargar una imagen, proporcionar una descripción de la misma e ingresar su nombre de usuario. Enviar un formulario de este tipo podría dar como resultado una solicitud similar a esta:

```
POST /images HTTP/1.1 
Host: normal-website.com 
Content-Length: 12345 
Content-Type: multipart/form-data; boundary=

---------------------------012345678901234567890123456 ---------------------------012345678901234567890123456 

Content-Disposition: form-data; name="image"; filename="example.jpg" 
Content-Type: image/jpeg 

[...binary content of example.jpg...] 
---------------------------012345678901234567890123456 

Content-Disposition: form-data; name="description"

This is an interesting description of my image. 

---------------------------012345678901234567890123456 
Content-Disposition: form-data; name="username" 

wiener
---------------------------012345678901234567890123456--`

```

Como puede ver, el cuerpo del mensaje se divide en partes separadas para cada una de las entradas del formulario. Cada parte contiene un `Content-Disposition`encabezado, que proporciona información básica sobre el campo de entrada con el que se relaciona. Estas partes individuales también pueden contener su propio encabezado `Content-Type`, que le indica al servidor el tipo MIME de los datos que se enviaron mediante esta entrada.

## Validación de tipo de archivo defectuosa - Continuación

Una forma en que los sitios web pueden intentar validar la carga de archivos es verificar que este encabezado específico de entrada coincida con un tipo MIME esperado `Content-Type`. Si el servidor solo espera archivos de imagen, por ejemplo, es posible que solo permita tipos como `image/jpeg`y `image/png`. Pueden surgir problemas cuando el servidor confía implícitamente en el valor de este encabezado. Si no se realiza ninguna validación adicional para verificar si el contenido del archivo realmente coincide con el supuesto tipo MIME, esta defensa se puede eludir fácilmente utilizando herramientas como Burp Repeater

# Caso 12: carga de shell web mediante omisión de restricción de tipo de contenido

Esta práctica de laboratorio contiene una función de carga de imágenes vulnerables. Intenta evitar que los usuarios carguen tipos de archivos inesperados, pero se basa en verificar la entrada controlable por el usuario para verificar esto.

Para resolver la práctica de laboratorio, cargue un shell web PHP básico y utilícelo para filtrar el contenido del archivo `/home/carlos/secret`. Envíe este secreto usando el botón provisto en el banner del laboratorio.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

Ya una vez hemos ingresado en la cuenta tenemos las mismas opciones del reto anterior, en esta ocasión hay algo que nos va a impedir subir el archivo y debemos hacer una breve modificación

![[Pasted image 20240325113755.png]]

![[Pasted image 20240325114039.png]]
![[Pasted image 20240325114101.png]]

Podemos notar con el Content-Type: application/octec-stream es un tipo de contenido genérico que indica que el archivo enviado es un flujo de bytes sin procesar, es decir, un archivo binario.

Cuando el servidor envía este tipo de contenido, el navegador no intentará interpretarlo o mostrarlo como una página web normal. En su lugar, el navegador puede solicitar al usuario que descargue el archivo o, dependiendo de la configuración del navegador y el tipo de archivo, intentar abrirlo con una aplicación asociada.

Este tipo de contenido se utiliza comúnmente para enviar archivos binarios como imágenes, audio, video, archivos ejecutables, documentos comprimidos, entre otros, que no se procesarán como contenido HTML o texto plano en el navegador, sino que se manejarán como datos binarios para su descarga o ejecución fuera del navegador

![[Pasted image 20240325114207.png]]

Pasamos esto al repetidor y podemos ver que nos da un aviso de que el archivo cargado no puede ser permitido dado que se espera que lo que se ingresen sean imágenes ciertos formatos

![[Pasted image 20240325114242.png]]

Hacemos un cambio para que el archivo .php pueda ser aceptado cambiamos el Content-Type: image/jpeg

![[Pasted image 20240325114320.png]]
![[Pasted image 20240325114336.png]]

Ya una vez alterado el envío nos aparece el avatar con el archivo como imagen, nos dirigimos a la ruta y de acuerdo al script generado nos lleva al usuario carlos y encontramos el código secreto

![[Pasted image 20240325120214.png]]
![[Pasted image 20240325120248.png]]
