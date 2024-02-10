## 1. Recolección de información 

Usando el comando *Netdiscover* para conocer las IP's que están dentro de nuestra red encontramos la maquina a atacar 

![[Pasted image 20240130013007.png]]

Ahora ya que encontré la maquina a atacar, haré un escaneo de puertos con *Nmap* para ver que servicios se ejecutan y que podemos obtener de ellas 

![[Pasted image 20240130014017.png]]

Tenemos dos puertos en ejecución, puerto 22/ssh y 80/http, por encima puedo ver que la pagina web está hecha en WordPress, aun así se debe hacer una enumeración con *gobuster* o *dirb* para saber cuantas rutas podemos encontrar con la dirección web

![[Pasted image 20240130014800.png]]

Ya una vez con las rutas identificadas solo falta revisar que puedo encontrar en cada una de ellas para posibles pistas
Tenemos una pagina y en ella podemos ver que alguien en su momentos posteó algo y fue el admin

![[Pasted image 20240130015649.png]]

Tenemos un login y haciéndonos a la idea de que el admin publicó algo en la pagina es seguro que puede que el username de la login sea admin, la contraseña la iremos probando, pero por ahora será 

	Username: admin 
	Password: admin

Para suerte las credenciales que puse terminaron siendo correctas y me dio acceso al login de wordpress

![[Pasted image 20240130020220.png]]
![[Pasted image 20240130021828.png]]
## 2. Análisis de vulnerabilidades

Dentro de todo lo que encontré quise pasar una archivo .zip por la ubicación de plugin pero me generaba un error, así que busqué por metasploit un exploit para subir un archivo con un payload de reverse-shell, termino de llenar los datos que me pide y ejecuto el exploit y accedo al sistemas

	 Set Username admin
	 Set Password admin
	 Set Rhosts 192.168.1.240

![[Pasted image 20240130154645.png]]
## 3. Explotación 

![[Pasted image 20240130154735.png]]

Obtengo la flag de usuario y comienzo a buscar dentro de wp-config.php credenciales para poder elevar privilegios

![[Pasted image 20240130155125.png]]

Revisando el wp-config.php encuentro credenciales de root y una contraseña, la probé para subir privilegios de root pero no funcionó.

![[Pasted image 20240130160010.png]]

Revisando los usuarios que aparecen dentro /etc/passwd, así que utilizo la contraseña que encontré en uno de los dos usuarios y tuve el acceso a *BLA*, quise probar suerte en *BLA1* pero cambié la contraseña de `bla_is_my_password` a `bla1_is_my_password` y me dió acceso a bla1

![[Pasted image 20240130160047.png]]

Esta misma contraseña me sirvió para tener acceso al ssh, ahora buscando el como subir privilegios encuentro que ck00 puede correr un comando para acceder a ck00

![[Pasted image 20240130202644.png]]
![[Pasted image 20240130203949.png]]
## 4. Post-Explotación 

Ya dentro del usuario ck vemos una opción de poder acceder al usuario root, así que comiendo a investigar de que trata esto.

![[Pasted image 20240130204349.png]]

Ya en ese momento ingreso un acceso de que el usuario ck-00 pueda ser enviado a root si lo desea. Una vez esta información termina en el archivo /etc/sudoers  ingreso el comando de sudo al usuario actual y soy enviado a root, me dirijo a la carpeta root y consigo la flag.

![[Pasted image 20240130215949.png]]
![[Pasted image 20240130220018.png]]

