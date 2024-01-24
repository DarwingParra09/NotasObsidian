## 1. Recolección de Información

Bueno, para esta maquina utilizaré los comandos que mejor son usados para el escaneo de redes, para esta vez uso el comando *Netdiscover*

![[Pasted image 20240123182514.png]]

Ya identificada la red pues utilizo el escaneo con *Nmap* para ver que puertos pueden estar abiertos y encontrar vulnerabilidades dentro de los puertos 

![[Pasted image 20240123183651.png]]

Bueno de paso dentro del escaneo veo que trabaja con una pagina web, asi que comenzaré a indagar dentro de la dirección y ver que hay

![[Pasted image 20240123190804.png]]

Cuando accedo a nagios encuentro un login, al principio quise ver que habia alguna vulnerabilidad por medio de injeccion sql, pero no.  

![[Pasted image 20240123190904.png]]

## 2. Análisis de vulnerabilidades

Seguí intentando y bueno nagios parece ser un plugin, asi que voy metasploit y busco si hay algún exploit o algo referente
Aquí tuve cierto problemas porque los exploit que estaban en metasploit no estaban funcionando, así que tuve que buscar un solución hacia esta vulnerabilidad, parece que dentro de la nueva actualización de mestaploit hicieron cambios en los nombres y por eso no encontraba el exploit especifico.

![[Pasted image 20240123231637.png]]

Ya cuando lo encuentro pues comienzo a hacer los cambios pertinentes para que el exploit haga lo suyo

	 Set LHost 192.168.1.214
	
	 Set RHost 192.168.1.6
	 
	 Set Password admin

![[Pasted image 20240123232430.png]]


## 3. Explotación
## 4. Post-Explotación

Obtengo la root y con ello la flag

![[Pasted image 20240123233134.png]]