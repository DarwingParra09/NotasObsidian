## 1. Recolección de información 

Con el comando *Netdiscover* busco la IP a la cual haré el ataque

![[Pasted image 20240126154544.png]]

Hago un escaneo en *Nmap* para ver que puertos pueden estar en ejecución

![[Pasted image 20240126154938.png]]

Veo que el puerto 80 está en servicio, así que opto por ingresar y ver que puede encontrarse. Antes de eso pienso hacer un escaneo de enumeración de directorios.

![[Pasted image 20240126160744.png]]

Ingreso al index y me da una respuesta efectiva dentro de la pagina, como siempre busco dentro del codigo fuente por si hay alguna pista y efectivamente tenemos un usuario

![[Pasted image 20240126161600.png]]
![[Pasted image 20240126161905.png]]

Seguimos buscando dentro de la otras rutas, la que veré a continuación es la ruta /robots y encuentro un mensaje encriptado, creí en su momento que era un hash, pero no, es un mensaje encriptado de base64.

![[Pasted image 20240126162139.png]]
![[Pasted image 20240126163130.png]]

Hemos conseguido la primera, ahora voy a investigar la otra ruta /hacker 

![[Pasted image 20240126173706.png]]
## 2. Análisis de vulnerabilidades

Parece ser que el mismo mensaje traiga consigo la contraseña ssh, asi que solo queda ingresar con el usuario y digital la contraseña 

![[Pasted image 20240126163130.png]]

## 3. Explotación

![[Pasted image 20240126174959.png]]

Tengo la siguiente flag y está en binario, así que busco por medio de la web algo que me convierta el binario en texto

![[Pasted image 20240126175154.png]]
![[Pasted image 20240126175436.png]]
## 4. Post-Explotación

Para su momento creí que el usuario root lo podria llegar a acceder con la flag2, pero no.
Paso mi archivo linpeas.sh al ssh dentro de la ruta /tmp y le doy permisos para poder utilizarlo como archivo ejecutable

![[Pasted image 20240126220339.png]]

Me muestra que hay una versión de Linux que puede ser utilizada para la escalación de privilegios, indago un como más en exploit data base y encuentro el exploit para ejecutar

![[Pasted image 20240126220413.png]]
![[Pasted image 20240126220453.png]]

Descargo el exploit al ssh con la intención de ejecutarlo y poder acceder al root, una vez descargado utilizo el comando `gcc overlayfs.c -o overlayfs` para compilar el programa fuente `overlayfs.c` utilizando el compilador GNU Compiler Collection (GCC) y generar un ejecutable llamado `overlayfs`, obtengo el usuario root y con ello la flag

![[Pasted image 20240126221905.png]]
![[Pasted image 20240126221959.png]]
