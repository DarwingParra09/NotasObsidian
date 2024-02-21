## 1.  Recopilación de información

Con el uso de netdiscover encuentro la red a analizar

![[Pasted image 20240219153624.png]]

Identificada la red a analizar utilizo la herramienta nmap para ver que puertos están abiertos

```
sudo nmap 192.168.1.274 -sSV --min-rate 5000 -p- -A
```

![[Pasted image 20240219161714.png]]

Al revisar el puerto 80 encuentro una pagina web 

![[Pasted image 20240219163512.png]]

Utilizando la herramienta Gobuster puedo indentificar que otras rutas hay en base a la direccion 

```
gobuster dir -u http://192.168.1.247 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,jgp,txt,bak
```

![[Pasted image 20240219173415.png]]
![[Pasted image 20240219173943.png]]
![[Pasted image 20240219174014.png]]

La ruta Dict traía una lista de palabras, posiblemente hay un usuario con el cual hacer un ataque de diccionario para saber su contraseña 

![[Pasted image 20240219174457.png]]

## 2. Análisis de vulnerabilidades

Teniendo el nombre y la versión de la pagina pude encontrar un exploit en Msfconsole

![[Pasted image 20240219173943.png]]

Veo que el exploit en msfconsole necesita un usuario y contraseña. Como no tenga esa información por ahora pues haré un ataque de fuerza bruta con el diccionario que encontré hacia los puertos 21 y 22

![[Pasted image 20240219232913.png]]

Haciendo un ataque de fuerza bruta obtengo la contraseña para el puerto 21 y con ello obtenemos la primera flag

```
hydra -l hacksudo -P dict.txt ftp://192.168.1.274
```

![[Pasted image 20240220021943.png]]
![[Pasted image 20240220023224.png]]
![[Pasted image 20240220023335.png]]

Dentro de la misma ruta tengo una carpeta a la cual accedo y encuentro otros archivos, así que me traigo autores.txt y el archivo zip

![[Pasted image 20240220025025.png]]
![[Pasted image 20240220025155.png]]

El archivo zip encontrado tiene consigo unos documentos pero está seguro con un contraseña, así que utilizando john de ripper conseguiré encontrar la contraseña y desencriptar el archivo

![[Pasted image 20240220031114.png]]
![[Pasted image 20240220030403.png]]

Ahora con la herramienta descargada que se llama SoundStegno puedo encontrar un mensaje oculto en el audio que tenia archivo zip

![[Pasted image 20240220034432.png]]
![[Pasted image 20240220034329.png]]
![[Pasted image 20240220034802.png]]

Miro el archivo de texto que trae el zip y me da la siguiente pista a trabajar con la dirección que acabo de encontrar.

![[Pasted image 20240220035023.png]]
![[Pasted image 20240220175925.png]]


## 3. Explotación

Una vez edito el archivo que contiene una shell reversa, lo siguiente a hacer es ponerme en el puerto de escucha y enviar el archivo a la pagina admin y ejecuto el archivo para tener acceso

![[Pasted image 20240220175450.png]]

Comienzo a indagar entre el sistema y encuentro una segunda flag

![[Pasted image 20240220181103.png]]
![[Pasted image 20240220182150.png]]
![[Pasted image 20240220182823.png]]

Utilizando de nuevo la fuerza bruta con hydra consigo la contraseña del usuario isro e ingreso a ese usuario

![[Pasted image 20240220183316.png]]
![[Pasted image 20240220183439.png]]

## 4. Post-Explotación

Dentro de la escalada de privilegio encuentro una carpeta llamada fog, esta tiene un ejecutable con python2 e ingreso el comando para acceder al usuario root y obtener la flag

![[Pasted image 20240220185430.png]]
![[Pasted image 20240220185523.png]]
