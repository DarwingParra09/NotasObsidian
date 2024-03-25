## Que es path traversal?

El recorrido de ruta también se conoce como recorrido de directorio. Estas vulnerabilidades permiten a un atacante leer archivos arbitrarios en el servidor que ejecuta una aplicación. Esto podría incluir:

- Código y datos de la aplicación.
- Credenciales para sistemas back-end.
- Archivos sensibles del sistema operativo.

En algunos casos, un atacante podría escribir en archivos arbitrarios en el servidor, lo que le permitiría modificar los datos o el comportamiento de la aplicación y, en última instancia, tomar el control total del servidor.

## Lectura de archivos arbitrarios mediante Path Traversal

Imagine una aplicación de compras que muestra imágenes de artículos a la venta. Esto podría cargar una imagen usando el siguiente HTML:

`<img src="/loadImage?filename=218.png">`

La `loadImage`URL toma un `filename`parámetro y devuelve el contenido del archivo especificado. Los archivos de imagen se almacenan en el disco en la ubicación `/var/www/images/`. Para devolver una imagen, la aplicación agrega el nombre de archivo solicitado a este directorio base y utiliza una API del sistema de archivos para leer el contenido del archivo. En otras palabras, la aplicación lee la siguiente ruta de archivo:

`/var/www/images/218.png`

Esta aplicación no implementa defensas contra ataques de recorrido de ruta. Como resultado, un atacante puede solicitar la siguiente URL para recuperar el `/etc/passwd`archivo del sistema de archivos del servidor:

`https://insecure-website.com/loadImage?filename=../../../etc/passwd`

Esto hace que la aplicación lea desde la siguiente ruta de archivo:

`/var/www/images/../../../etc/passwd`

La secuencia `../`es válida dentro de una ruta de archivo y significa subir un nivel en la estructura del directorio. Las tres `../`secuencias consecutivas avanzan desde `/var/www/images/`la raíz del sistema de archivos, por lo que el archivo que realmente se lee es:

`/etc/passwd`

En los sistemas operativos basados ​​en Unix, este es un archivo estándar que contiene detalles de los usuarios registrados en el servidor, pero un atacante podría recuperar otros archivos arbitrarios utilizando la misma técnica.

En Windows, ambos `../`y `..\`son secuencias válidas de recorrido de directorio. El siguiente es un ejemplo de un ataque equivalente contra un servidor basado en Windows:

`https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini`

# Caso 1: Simple Path Traversal

![[Pasted image 20240317221457.png]]

Esta pagina maneja el caso de path traversal, así que teniendo como referencia los ejemplos de como acceder a /etc/passwd busco dentro de la pagina algo en la URL que me de una pista, comienzo a revisar los catálogos y veo que las imágenes están siendo llamadas desde un carpeta, aqui es donde utilizo la secuencia y obtengo la información de las contraseñas, claro está que no se pueden divisar por el navegador tan fácilmente, así que utilizo la herramienta de *Burpsuite* y en el response me da lo que estaba buscando

![[Pasted image 20240317221406.png]]

