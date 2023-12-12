
## 1. Recopilación de Información

Dentro de la maquina a trabajar esta vez utilizo el comando **NETDISCOVER** para identificar la dirección nueva encontrada. IP 192.168.1.217 

![[Pasted image 20231205002443.png]]

Una vez encontrada la IP pues buscamos que puertos pueden estar abiertos dentro de esta red y si hay dentro de esta dirección. 

![[Pasted image 20231205234308.png]]

Dentro de la búsqueda de puertos podemos ver que tenemos el puerto 22,80,8080,9000 abiertos, dentro de lo que son los puertos 80 y 8080 podemos ver que el entrar en la pagina nos devolvería un estado 403, 404 que quiere decir que no se encuentra información.

![[Pasted image 20231205202753.png]]

Dentro del puerto 9000 encuentro la siguiente información, nos habla sobre una base de datos llamada Bolt, por ahora tomaré esta información para después. 

![[Pasted image 20231205234438.png]]

Aun haciendo enumeración no encontramos rutas que nos dieran mucho información, así que probé la ruta que es como más conocida y que posiblemente pueda traer alguna pista **/robots.txt**

![[Pasted image 20231205234728.png]]

![[Pasted image 20231205234819.png]]

Aun así tampoco pude encontrar mayor cosa.

Dentro del puerto 8999 encontramos ciertos archivos del cual percato que hay uno que trata de una captura de red. Lo voy a descargar y ver que contiene

![[Pasted image 20231206000113.png]]

## 2. Analísis de Vulnerabilidades

![[Pasted image 20231206002436.png]]

Una vez descargado voy a buscar información por medio de WireShark. Parece ser que dentro de la red que trabaja el servidor está el nombre `dlink` como red asociada dentro de un entorno Inalámbrico

![[Pasted image 20231206005355.png]]

## 3. Explotación 

Lo siguiente en hacer es encontrar una contraseña, ya que al saber  dlink es una conexión inalámbrica pueda llegar a interactuar con el SSH, hacer un ataque de fuerza bruta y encontrar una contraseña usaré `aircrack-ng` 

![[Pasted image 20231206011401.png]]

Encontrada ya la contraseña con la herramienta Aircrack-ng podemos continuar y ver si podemos tener acceso al servicio del puerto 22 que es el SSH

![[Pasted image 20231206013812.png]]

## 4. Post-Explotación

Pasando al siguiente paso de escalada de privilegios, decidí verificar los binarios **SETUID** usando el comando:

find/ -perm -4000 -user root 2>/dev/null

Había muchos binarios SETUID en esta máquina, pero uno de los poco comunes es **/usr/bin/nohup** . Así que decidí comprobar **GTFObins** si tiene una carga útil privada usando **nohup** 

![[Pasted image 20231206021950.png]]
