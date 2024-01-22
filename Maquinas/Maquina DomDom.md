## 1. Recopilación de información

Comenzamos analizando las redes que están dentro de nuestra red, Utilizando el comando *netdiscover* vemos las múltiples IP's 

![[Pasted image 20240117205320.png]]

La IP nueva que vemos en la que tiene como terminación 229, es decir 192.168.1.229, a esta red se le hará un escaneo de puertos para identificar que servicios está corriendo y que posibles vulnerabilidades se pueden encontrar de ellos.

Utilizo el comando *Nmap* para ver los puertos abiertos y puedo ver que solo tenemos el servicio http, así que ya que sabemos que tenemos una pagina web, ahora buscaré dentro de la pagina alguna información que me puede ser de utilidad, también de paso haré una enumeración con Gobuster y Dirb para ver que otras rutas hay dentro de la dirección IP 

![[Pasted image 20240117210409.png]]

Al ingresar en la pagina web encuentro una especie de formulario para poder llenar con la intención de ingresar a la pagina, pero antes de eso voy a hacer una enumeración  

![[Pasted image 20240117211456.png]]
![[Pasted image 20240117213347.png]]
![[Pasted image 20240117213415.png]]
![[Pasted image 20240117213725.png]]

No veo mucho movimiento dentro de esta pagina, la única iniciativa que tengo es trabajar con burp suite para saber que es lo que hace la acción de ingresar los datos que se dan al formulario

![[Pasted image 20240117215509.png]]

Indagando dentro del repetidor de Burp Suite puedo ver que no hay ninguna reacción al enviar la petición dentro del index.php, mas sin embargo al cambiar el encabezado de index.php a admin.php tengo la siguiente acción.

![[Pasted image 20240117233745.png]]

En lo poco que se alcanza a ver me muestra que la acción es ejecutada por el cmd o terminal, si este es el caso podría mandar una reverse-shell para poder entrar al sistema
## 2. Análisis de vulnerabilidades

![[Pasted image 20240117234315.png]]

tal como se pensaba, pues envío un comando por cmd que me identifique el id, la respuesta viene de la siguiente manera `uid=33(www-data) gid=33(www-data) groups=33(www-data),27(sudo)`, esto nos termina de confirmar que es vulnerable a una ejecución remota de código, lo que haré entonces es utilizar un archivo que maneja una shell inversa, abriré un servidor con python y haré la descarga de tal archivo por el Burp Suite para que sea ejecutado
## 3. Explotación

![[Pasted image 20240117235301.png]]

Mientras vamos editando el archivo para la reverse-shell voy colocando en otra terminal un puerto de escucha por el puerto 4445, para que todo salga según lo planeado necesito abrir un servidor y hacer la petición

![[Pasted image 20240117235858.png]]

![[Pasted image 20240118000146.png]]

Una vez ya tengo tanto la dirección con el puerto de escucha abierto, pues me dirijo a Burp Suite y hago la petición de descargar por medio del cmd el archivo llamada reverse_shell que se aloja en la dirección ip 192.168.1.229, seguido de esto le doy permisos para que pueda ser ejecutado por cmd y eso es lo que hará que dentro de un momento funcione y entremos en el sistema 

![[Pasted image 20240118001854.png]]

Aquí ya ejecuto el archivo por cmd y como ya tenían el puerto de escucha pues ya me indica que estoy dentro del sistema

![[Pasted image 20240118002512.png]]

![[Pasted image 20240118001748.png]]

Encuentro un usuario de nombre domom, comienzo a ver sus directorios y  encuentro en escritorio un archivo *Readme.md* pero veo que tiene privilegios de root, asi que utilizo el comando `getcap -r / 2>/dev/null` para buscar y mostrar las capacidades de los archivos binarios que tienen capacidades de Linux Extended Attributes (atributos extendidos de Linux) en el sistema de archivos raíz ("/") y sus subdirectorios recursivamente.

![[Pasted image 20240118004915.png]]

me voy a /tmp y comprimo desde allí el archivo readme.md, después de esto busco descomprimirlo y me da la ubicacion del readme que yo cree y que puedo leer, con el comando cat y sin moverme de la carpeta /tmp voy hacia la ruta del archivo que busco y me da la contraseña root

## 4. Post-Explotación

![[Pasted image 20240118010240.png]]
Una vez ya tengo la contraseña pues ingreso como usuario root y obtengo la flag

![[Pasted image 20240118011458.png]]


