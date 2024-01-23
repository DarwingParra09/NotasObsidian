## 1. Recolección de Información

Para esta maquina podemos buscar la dirección IP de dos maneras: *Netdiscover* y *Nmap*, para esta ocasión usaré **Netdiscover** y voy a indicar la red a atacar.

![[Pasted image 20240122011157.png]]

Una vez indicada la red a atacar pues haré un escaneo de puertos con *Nmap* y poder determinar que herramienta puede llegarme a ser de utilidad para la hora de ingresar al sistema. 

![[Pasted image 20240122011936.png]]

Dentro del escaneo encuentro tres servicios en ejecución, el puerto 22/ssh, el puerto 80/http y el puerto 21/ftp, por el momento con esta información puede sernos útil las herramientas de enumeración de directorios como lo es *Gobuster* y *Dirb*.

![[Pasted image 20240122012334.png]]
![[Pasted image 20240122012525.png]]

En los escaneos hechos con las dos herramientas tenemos las mismas rutas dentro de la pagina web así que investigaré que trae cada una de las rutas encontradas.
Dentro de la pagina principal encuentro un mensaje de una persona de nombre Annlynn, dice ser que fue la causante del ataque al servidor y quiere que la encuentre, busco dentro del código fuente de la pagina y hay una nota 

![[Pasted image 20240122014004.png]]
![[Pasted image 20240122014322.png]]

Dentro de la siguiente ruta /robots.txt me dice que hay una ruta permitida que se llama /nothing, la misma ruta que en la enumeración nos mostraba

![[Pasted image 20240122014743.png]]
![[Pasted image 20240122015013.png]]
![[Pasted image 20240122020808.png]]
![[Pasted image 20240122020836.png]]

No habia detallado bien la ejecucion del comando Gobuster y estaba perdiendo una ruta, con la nueva ruta identificada buscaré que información puede servirme, por ahora no he encontrado algo muy relevante

![[Pasted image 20240122024727.png]]

Entrando en la ruta encuentro más rutas a investigar 

![[Pasted image 20240122133035.png]]

Dentro de todas las rutas la única con un resultado fue /pwned.vuln, me deja un mensaje diciéndome 
que esta ruta también fue hackeada con un método avanzado, dentro del codigo fuente encuentro otra pista dejada por el hacker

![[Pasted image 20240122133852.png]]
![[Pasted image 20240122134515.png]]
## 2. Análisis de Vulnerabilidades

Resuelvo entrar en el puerto FTP con las credenciales dadas por el hacker y obtengo entrada al sistema

![[Pasted image 20240122135509.png]]

Dentro del usuario ftpuser encuentro una carpeta llamada share con una nota y lo que es una contraseña ssh privada, llevo estos archivos a mi directorio local y sigo investigando.

![[Pasted image 20240122140053.png]]

Buscando encuentro más usuarios los cuales me imposibilitan el acceso a las carpetas, aun así hay un archivo ejecutable que me envío a mi directorio local para comenzar a ver de que trata cada cosa

![[Pasted image 20240122140137.png]]

Ya dentro de mi directorio comienza a revisar los archivos extraídos y empieza a ver de que trata cada cosa

![[Pasted image 20240122141400.png]]
## 3. Explotación

Le damos permiso de escritura y lectura a la clave ssh, por lo que decía la nota parece ser que esta clave es de Ariana, así que haré la conexión a su nombre

![[Pasted image 20240122222620.png]]

Y como esperaba, la clave ssh es de ariana y esta me dió acceso a su carpeta de usuario 

![[Pasted image 20240122223351.png]]

Bueno, dentro de la carpeta encontramos la primera flag y una desahogo de ariana en base a una pelea que tuvo con alguien del personal, esto fue lo que provocó que se pudiera conocer la ruta /hidden_text

![[Pasted image 20240122223757.png]]

Salté una parte, estaba buscando como podía elevar privilegios y me encuentro con que selena tenia permitido el acceso al ejecutable messenger.sh y con el subo privilegios y ahora estoy en su usuario

![[Pasted image 20240122224805.png]]

Encuentro  la flag de selena y un mensaje que aunque no tiene importancia en estos momentos fue causante de que se encontrara una vulnerabilidad con la llave privada ssh

![[Pasted image 20240122224957.png]]
## 4. Post-Explotación

Encuentro que selena trabaja con docker y podría buscar la manera de escalar privilegios de root y terminar con la búsqueda de la flag.
Encuentro una imagen privada, así que busco dentro de la pagina GTFObins un comando para poder acceder al root, edito el archivo de imagen y con ello obtengo el acceso a root y con ello la flag 

![[Pasted image 20240122225852.png]]
![[Pasted image 20240122230042.png]]
![[Pasted image 20240122230302.png]]
