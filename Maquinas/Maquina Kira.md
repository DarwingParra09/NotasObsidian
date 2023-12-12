## 1. Recopilación de Información / Enumeración

Utilizamos el comando ``sudo netdiscover`` con la utilidad de poder ver que dispositivos están trabajando dentro de nuestra red , con ellos podemos identificar la dirección IP 192.168.1.215.

![[Pasted image 20231128143434.png]]

Cabe resaltar que estamos trabajando con una maquina Linux, para poder darnos cuenta de ello solo basta con ver el TTL al hacer ping a la dirección IP.

![[Pasted image 20231128143531.png]]

Las TTL mas conocidas de los sistemas operativos son las siguientes

- Linux/Unix: 64
- Windows: 128
- MacOS: 64
- Solaris/AIX: 254
- FreeBSD: 64

Una vez identificada la red la cual vamos a atacar, buscaremos los puertos abiertos que puedan encontrarse de la dirección, usaremos *Nmap* para este tipo de escaneo

![[Pasted image 20231129200507.png]]

Encontramos el puerto 80/TCP abierto, este servicio es para paginas web Http, ya en su momento veré de que trata la pagina, ahora recolectaré más información de rutas teniendo en cuenta la dirección en base a pagina web.

Usaremos el comando `dirb` con la dirección que encontramos y nos muestra la siguiente información en base a rutas

![[Pasted image 20231129200228.png]]

![[Pasted image 20231129214247.png]]

Dentro de la ruta que encontramos podemos ver un archivo en donde al parecer se guardan archivos de todo tipo. Posiblemente nos puede servir en su momento para hacer una reverse shell

![[Pasted image 20231129221642.png]]

En el archivo principal encontramos la opción de lenguaje y cargar, si es lo que estoy pensando seria una sesión para subir archivos, así que podemos hacer una reverse shell para entrar al sistemas.

![[Pasted image 20231129221923.png]]

## 2. Análisis de vulnerabilidades 

Dado que encontramos una opción de enviar archivos podremos utilizar una reverse shell para entrar a la maquina y encontrar los flag

![[Pasted image 20231129222546.png]]

Ya creado nuestro archivo, vamos a cargarlo en la pagina y revisarlo para saber si llego bien el archivo

![[Pasted image 20231129224351.png]]

Al parecer no podemos enviar un archivo de cualquier tipo, solo nos deja enviar archivos tipo imagen, así que no se puede hacer el direccionamiento con un archivo PHP. Así que guardaré el archivo con doble tipo 

![[Pasted image 20231129224744.png]]

Dado que no se puede ejecutar el archivos por medio de la ruta upload, pues tendré que buscar otra opción, volviendo a la pagina principal encuentro la opción de lenguaje, al dar click varias veces encuentro un cambio en la ruta, el cual es el siguiente : 

![[Pasted image 20231129225049.png]]

Podemos observar las siglas LFI, pero que quiere decir esto:

### LFI

LFI son las siglas de "Local File Inclusion" (Inclusión de Archivos Locales, en español). Se refiere a una vulnerabilidad de seguridad que ocurre cuando una aplicación web permite a un atacante incluir archivos locales de un sistema en las páginas web generadas dinámicamente.

Esta vulnerabilidad puede surgir cuando una aplicación web utiliza la entrada del usuario para construir rutas de archivos que se incluyen en las páginas web sin la debida validación o sanitización. Si un atacante puede manipular estas rutas de archivos de alguna manera, puede lograr la inclusión de archivos locales no autorizados, lo que puede tener consecuencias graves.

Un ataque de LFI podría permitir que un atacante acceda y lea archivos del sistema, incluyendo archivos de configuración sensibles, registros del sistema, o cualquier otro archivo al que el servidor web tenga acceso. En algunos casos, esto podría llevar al compromiso de la seguridad del sistema y al acceso no autorizado.

Para prevenir ataques de LFI, es importante validar y sanitizar adecuadamente todas las entradas de usuario que se utilizan para construir rutas de archivos, y limitar el acceso a archivos sensibles tanto como sea posible. Además, es recomendable utilizar mecanismos como listas blancas (whitelists) en lugar de listas negras (blacklists) para especificar qué archivos o rutas son permitidos, ya que las listas negras pueden ser menos efectivas y más propensas a errores.


Dado a la siguiente información y a las siglas que nos presenta la pagina quiere decir que dentro de la ruta en la que estamos hay una vulnerabilidad, asi que trabajaré con el Path Traversal (Recorrido de Ruta), como sabemos que estamos por medio de una pagina web la ubicacion precisa en donde podemos encontrar nuestro archivo montado seria el siguiente. ../../../../../../../../../../var/www/html/uploads/reverse_shell.php.jpg 

![[Pasted image 20231129230605.png]]

**Nota** : en un terminal aparte abriremos un oyente en Netcat para esperar la ejecucion de la reverse_shell 

![[Pasted image 20231129230625.png]]

## 3. Explotación 

Ya en este momento monté un nuevo documento que descargué que es para un reverse shell en php, el documento lo accederé por medio de path traversal y me pondré en escucha al momento de ejecutar la ruta en la pagina.

![[Pasted image 20231130174526.png]]

Teniendo ya claro el archivo subido pues busco ejecutarlo por medio del path traversal y obtengo la reverse shell

![[Pasted image 20231129230605.png]]

![[Pasted image 20231129233229.png]]

Ya una vez dentro pues comienzo a buscar en la raiz para llegar con más facilidad a la ruta del html, encuentro unas carpetas y me dirijo la que tiene como nombre **Supersecret-for-aziz**  y dentro de ella hay un archivo que parece contener la contraseña de un usuario llamado bassam.

![[Pasted image 20231129233827.png]]

Ya con la contraseña encontrada busco tener acceso al usuario bassam y dentro de la carpeta raiz hay un texto llamado user.txt, esta seria la primera flag encontrada

![[Pasted image 20231129234433.png]]
## 4. Post-Explotación 

Aquí en esta parte buscamos como acceder al acceso root, el comando que utilizamos para esta ocasión fue `sudo find ./bin/bash ;quit` con esto ya al escribir whoami nos indica que ya somos usuario root, solo nos queda ir a la carpeta root y buscar la flag que nos faltaria para terminar con el hackeo de la Maquina Kira 

![[Pasted image 20231129234934.png]]
