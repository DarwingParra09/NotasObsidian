## 1. Recopilación de información

En esta etapa comenzamos con el escaneo en Netdiscover para detectar las direcciones IP que están agregadas a nuestra red por el momento 

![[Pasted image 20240112192221.png]]

Una vez obtengo la direccion IP de la maquina a atacar, comienzo con el escaneo de puertos en Nmap para ver que posibles vulnerabilidades puedo encontrar dentro de los puertos abiertos

![[Pasted image 20240112193255.png]]

Ya una vez identificado los puertos abiertos observo que hay una pagina web en el puerto 80, así que primero indagaré en la pagina y haré una enumeración de directorios para ver si logro encontrar más información que me ayude a llegar a la flag.

![[Pasted image 20240112194321.png]]

Tenemos la siguiente pagina, la cual nos indica que es una Base de Datos de imagenes, me muestra redirecciones, pero buscaré con Gobuster o Dirb para ver si no me estoy perdiendo de algo importante

![[Pasted image 20240112195101.png]]

![[Pasted image 20240112195131.png]]

## 2. Analisis de Vulnerabilidades

Dentro de la ejecución del comando tanto en Gobuster como Dirb nos muestra las mismas rutas, me voy hacia la ruta Galería y encuentro una opción para la entrega de elementos que en este caso son imágenes, así que vamos a probar con una reverse Shell en formato de imagen para ver que sucede.

![[Pasted image 20240112195538.png]]
![[Pasted image 20240112195942.png]]
![[Pasted image 20240112200055.png]]
![[Pasted image 20240112200306.png]]
![[Pasted image 20240112200446.png]]
La reverse shell está configurada para funcionar por el puerto 4445, lo que debo hacer ahora es ponerme en modo escucha por ese puerto y ver si funciona, de no ser así encontré otra posibilidad de entrar por otro puerto

![[Pasted image 20240112202128.png]]

Aunque no tomé la evidencia bien, voy a explicar que hice, en la opción de seleccionar un achivo que viene a ser la imagen, activo el Burp Suite y lo siguiente que hago es editar la imagen y pasarla a un archivo .PHP
El resultado es el siguiente, como todavia sigo en escucha pues puedo ejecutar el archivo y eso ya me da acceso 

![[Pasted image 20240112202435.png]]

## 3. Explotación 

![[Pasted image 20240112202453.png]]

Me encuentro con un usuario llamado miguel, comenzaré a investigar para saber si me estoy perdiendo de algo

![[Pasted image 20240112203248.png]]

## 4. Post-Explotación

Todavia no he encontrado de miguel, pero mientras indagaba vi que tenemos acceso a /etc/passwd, asi que esto lo copiamos en la IP de la pagina y lo vemos como ruta ahora

![[Pasted image 20240112210557.png]]

Descargamos este archivo en nuestro maquina local y editamos un usuario y una contraseña

![[Pasted image 20240112213238.png]]

Digitamos lo siguiente con la explicacion de para que se usa:

![[Pasted image 20240112214154.png]]

Este comando utiliza el comando `openssl` para generar una contraseña cifrada utilizando el algoritmo de cifrado MD5 y un salt personalizado.

- `openssl passwd`: Este comando de OpenSSL se utiliza para generar contraseñas cifradas.
- `-1`: Indica que se utilizará el algoritmo de cifrado MD5. MD5 no se considera seguro para almacenar contraseñas en la actualidad debido a su vulnerabilidad a ataques de colisión, pero se puede utilizar en algunos sistemas heredados.
- `-salt asmo`: Especifica el salt para el cifrado. El salt (o sal) es una cadena aleatoria que se agrega a la contraseña antes de realizar el cifrado. En este caso, el salt es "asmo", pero puedes cambiarlo a cualquier cadena que desees.
- `newpassword`: La contraseña que deseas cifrar.

Luego de esto pasamos nuestro hash al documento passwd e iniciamos un servidor en python, como el siguiente

![[Pasted image 20240112215442.png]]

Dado a que no me salia el hash en el archivo tuve que ponerlo manual, ahora una vez inicio el servidor voy a mi reverse shell y desde la carpeta /tmp descargo el nuevo passwd y lo copio en la carpeta /etc/passwd

![[Pasted image 20240112215644.png]]

Ahora vamos ingresar con el usuario fabian y su contraseña y obtendré el usuario root

![[Pasted image 20240112231958.png]]

Obtenemos la root.txt 

![[Pasted image 20240112232049.png]]

De paso ya que somos root pues vamos a la carpeta del usuario Miguel y obtenemos la otra flag y con esto culmina la maquina Sunset-Twilight

![[Pasted image 20240112232721.png]]
