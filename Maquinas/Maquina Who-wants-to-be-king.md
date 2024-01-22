## 1. Recopilación de información 

Comenzamos analizando el sistema para encontrar la red la cual vamos a trabajar, usando en comando *Netdiscover* haremos el escaneo

![[Pasted image 20240116031231.png]]

Ahora utilizamos el comando *nmap*  para  ver los puertos abiertos e investigar si hay vulnerabilidades a simple vista

![[Pasted image 20240116032617.png]]

Ya encontrados los puertos abiertos vemos que hay un una pagina web con la dirección IP, lo que haré seguidamente en ingresar y ver si hay algo de valor en el código fuente, también manejaré las herramientas *Gobuster* y *Dirb* para hacer una enumeración directorios

![[Pasted image 20240116132255.png]]
![[Pasted image 20240116132315.png]]

No se encontró nada, al menos hay una pagina que nos deja un archivo, veré si se puede descargar para ver que trae o de que va

![[Pasted image 20240116133127.png]]
## 2. Análisis de Vulnerabilidades

Al parecer es un archivo ejecutable y como no podemos verlo por medio de *nano* o *cat* pues utilizamos el comando **strings** ya que este comando es útil principalmente para determinar los contenidos de ficheros que no sean de texto. Una vez ejecutamos el comando podemos ver que hay un enunciado con una frase o mensaje encriptado, parece ser que está cifrado en base64, así que será fácil ver el mensaje.

![[Pasted image 20240116144638.png]]

Desencriptamos el mensaje y nos da lo siguiente información

![[Pasted image 20240116150141.png]]

No sé si sea una contraseña o un nombre, asi que buscaré por la web si hay algo que tengo alguna similitud o coherencia con el mensaje que obtuve.

Dentro de la búsqueda encontré una similitud con la serie Juego de Tronos y entre ello un personaje principal de la serie

![[Pasted image 20240116150916.png]]
## 3. Explotación

Utilizamos la información obtenida para ver si algo de lo encontrado puede servirnos para abrirnos paso por el sistema.
Dentro de la información tenemos el nombre de un personaje ficticio y con ello un mensaje o petición que hacia el personaje en su serie. 
Revisando el escaneo de puertos vemos que hay un puerto por donde podes acceder al sistema y es el puerto 22/tcp SSH 

![[Pasted image 20240116152500.png]]

Probamos suerte con el nombre de pila de personaje y el mensaje desencriptado y obtenemos acceso al sistema, ahora debemos buscar las flag y subir los privilegios para encontrar la flag de root.

![[Pasted image 20240116152738.png]]

Pues por lo visto no hay una flag en el usuario, pero si encontramos un mensaje que dice `Find Home, Pls` nos dirigimos a los archivos ocultos y vemos la carpeta local con un archivo .zip y lo descomprimimos para encontrar una nota sobre una ruta donde encontraremos otra pista 

![[Pasted image 20240116155624.png]]

Nos ubicamos en la ruta y accedemos a la nota

![[Pasted image 20240116160316.png]]
viendo que estamos hablando de la serie de Juego de Tronos, puedo creer que habla de Khal Drogo, que es el esposo de Daenerys

![[Pasted image 20240116161002.png]]
## 4. Post-Explotación

Tras varios intentos di con la contraseña **khaldrogo**, nos identifica como los usuarios root y despues buscamos la carpeta root y obtenemos la flag. Con esto culmina la maquina.

![[Pasted image 20240116161604.png]]
