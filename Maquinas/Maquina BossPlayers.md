## 1. Recopilacion de informacion/Enumeracion 

En el inicio de nuestra maquina usamos el comando netdiscover para verificar dispositivos tenemos conectados en nuestra red.
![[Pasted image 20231214162922.png]]

Ya identificada nuestra red a examinar pues utilizamos el escaneo de nmap para ver los posibles puertos abiertos y que vulnerabilidades podemos llegar a encontrar

![[Pasted image 20231214162948.png]]

Con ayuda Gobuster y Dirb hacemos una enumeracion de la direccion ip en donde podemos encontrar otras rutas que tambien van acompañadas de la direccion web

![[Pasted image 20231214163255.png]]
![[Pasted image 20231214165034.png]]

Dentro de la pagina principal encontramos un mensaje de bienvenida el cual me describe de que va la maquina, no me quedo solo con lo que me muestra, asi que indago un poco más en su codigo fuente y encuentro un texto en las ultimas lineas, este texto puede ser un mensaje codificado asi que en su momento miro de que trata

![[Pasted image 20231214170011.png]]
![[Pasted image 20231214183301.png]]
![[Pasted image 20231214170031.png]]

Al parecer los textos encontrados venian codificados por base64, para la contraseña secreta fue un trolleo de parte del reto, pero dentro del primer texto encontrado pude detectar que se habia codificado el mensaje 3 veces y con ello encontré una ruta al final

![[Pasted image 20231214183603.png]]

Dentro de la ruta me dan unas especificaciones de que puedo tener un alcance por medio de comandos, asi que busco la forma de mandar una reverse shell y obtener acceso y corre

![[Pasted image 20231215054018.png]]

Una vez me pongo en escucha recorro el comando dentro de la pagina y obtengo acceso a la reverse shell 

![[Pasted image 20231215054756.png]]

Buscamos los archivos que tengan en ejecucion SUID activados

![[Pasted image 20231215055703.png]]
Pasamos a aumentar los privilegios y buscamos la carpeta root con su flag para culminar la maquina, la flag tambien está cifrada en base64 asi que la decodificamos y nos da las felicidades al culminarla

![[Pasted image 20231215055741.png]]
![[Pasted image 20231215055818.png]]
![[Pasted image 20231215055912.png]]
