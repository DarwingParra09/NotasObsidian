
## 1. Recopilación de información

Utilizando el comando Netdiscover conseguimos las IP que está siendo utilizadas dentro de nuestra red, para el caso de hoy tenemos una maquina Linux
![[Pasted image 20231212164224.png]]
Seguimos con el escaneo de puertos para poder ver que hay de interes y encontrar vulnerabilidades, tenemos los puertos 21,22,80,3306 
![[Pasted image 20231212164924.png]]

Dentro de la búsqueda de directorios encontré los siguientes, el único que me sirvió fue el del administrador y me pedía terminar de completar la configuración e instalación 

![[Pasted image 20231212164955.png]]

![[Pasted image 20231212165314.png]]

![[Pasted image 20231212165356.png]]

![[Pasted image 20231212165452.png]]

## 2. Anilisis de Vulnerabilidades

Revisando la maquina virtual a la que se debe hackear noté que al inicio ya hay un nombre de usuario asi que intento acceder con hydra con el puerto ssh y utilizando el archivo de texto rockyou y encuentro la contraseña de acceso

![[Pasted image 20231212172151.png]]
## 3. Explotación 

Me conecto por medio de ssh utilizando la contraseña hace un momento adquirida y tengo entrada, ahora solo me quedaba subir privilegios y llegar a la flag

![[Pasted image 20231212172351.png]]
## 4. Post-Explotación

Aqui ya tenemos el usuario root y con ello la flag, con esto termina la maquina W1r3s, creo que habia otra manera de hacerlo, pero pues esta fue directa

![[Pasted image 20231212172756.png]]
![[Pasted image 20231212172826.png]]


