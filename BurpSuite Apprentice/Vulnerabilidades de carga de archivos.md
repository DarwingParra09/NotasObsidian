
# Ejecución remota de código mediante carga de shell web

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

# Carga de shell web mediante omisión de restricción de tipo de contenido

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
