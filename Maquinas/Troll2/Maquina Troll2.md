
### Escaneo de red

Lo primero que haremos es identificar las direcciones IP que estén en nuestra red, para eso usaremos netdiscovery para poder hacer el análisis, lo ejecutamos con privilegios sudo

![[Pasted image 20231017002715.png]]
Una vez encontramos las direcciones IP, identificamos cual es la red nueva que encontrada en nuestra red, en esta ocasión es 192.168.150.132 

## Recopilacion de Informacion 

Utilizaremos Nmap para buscar toda la informacion pertinente sobre esta red, que puertos puede tener abiertos, los servicios, versiones, entre otras cosas

![[Pasted image 20231017003418.png]]

Podemos observar que esta IP trabaja con 3 puertos abiertos, 21 FTP, 22 SSH  y 80 HTTP.
Como sabemos que la dirección trabaja con una pagina web en el puerto 80 pues buscaremos analizarla para ver que podemos encontrar y llegar a las Flag 

## Analizando la Web 

![[Pasted image 20231017003846.png]]

Ingresamos a la pagina web y encontramos una imagen troll con un mensaje, de momento no vemos nada raro, así que buscaremos ver que hay dentro del código fuente.

![[Pasted image 20231017004021.png]]
Dentro del código fuente vemos que nos deja un mensaje y algo que nos puede llegar a servir en su momento es el nombre del autor, así que lo tendremos en cuenta más adelante

## Inspección en el Puerto 21 FTP

Ya que tenemos un nombre que es *Tr0ll*, veremos si dentro de el puerto 21 nos da respuesta

![[Pasted image 20231017004735.png]]
para nuestro suerte tanto el nombre como la contraseña son las mismas así que ya tenemos acceso al puerto 21 y veremos que podemos obtener de este.

![[Pasted image 20231017005038.png]]

La inspección del puerto 21 fue exitosa y con ella encontramos un archivo zip, utilizamos el comando *Get* para obtener el archivo en nuestro equipo, una vez obtenido el archivo salimos la conexion FTP

Buscamos el archivo zip que pasamos a nuestro equipo y lo descomprimimos

![[Pasted image 20231017021621.png]]
vemos que nos pide una contraseña y el nombre que nos nuestra es de un usuario llamado noob, también lo tendremos en cuenta 

## Enumeración de directorios

Ahora trabajaremos con la herramienta gobuster para poder hacer la enumeración y poder encontrar directorios ocultos 
![[Pasted image 20231017023955.png]]
Dentro de los archivos encontrados tenemos el archivo principal que es el index, ahora encontramos otros dos archivos llamado Robots que miraremos que tiene, los archivos con status 200 es a los que podemos ingresar, los que tiene asignado el valor 403 están restringidos

![[Pasted image 20231017024715.png]]
Dentro del archivo robots.txt encontramos la siguiente información, podemos ver que otras cosas hay si guardamos todos estos directorios en un archivo con el nombre que deseemos, pero que tenga referencia.

![[Pasted image 20231017031501.png]]
![[Pasted image 20231017031929.png]]
Podemos ver que tenemos 4 carpetas en las que encontramos diferentes tamaños, cada una se estarán revisando 
![[Pasted image 20231017033703.png]]

Descargamos las imágenes con el comando wget, con ello no perdemos metadatos a la hora de descargar. Una vez esto enlistamos y encontramos que una de las imágenes pesa más que las otras.

![[Pasted image 20231017034050.png]]

Ya que sabemos que una de las imágenes tiene un tamaño diferente pues miramos que información hay de diferente a las demás y encontramos un mensaje en las ultimas líneas

![[Pasted image 20231017155007.png]]
Colocamos la palabra y0ur_self a la dirección IP en nuestra pagina y averiguar si es otra directorio oculta, tenemos suerte y damos con un archivo llamado answer.txt, descargamos el archivo por la web con el comando `wget` 

![[Pasted image 20231017155633.png]]
![[Pasted image 20231017155908.png]]

## Decodificando el archivo answer

Una vez accedemos al archivo answer podemos ver que se repiten multiples caracteres, parece estar codificado en base64, lo que tenemos que hacer ahora es decodificar el archivo y pasarlo a un nuevo archivo

![[Pasted image 20231017160736.png]]
Utilizamos el comando `fcrackzip` para descomprimir el archivo zip y utilizamos las flag -u -D -p, de paso también agregamos el texto ya decodificado y seleccionamos el archivo que se va a descomprimir

![[Pasted image 20231020020722.png]]
Tenemos ya con nosotros la contraseña, la utilizaremos para descomprimir el archivo zip ya que tenemos la contraseña 

![[Pasted image 20231020021119.png]]

tenemos ahora un archivo llamado noob, al revisarlo encontramos una llave privada, eso nos servirá para poder conectar al puerto `ssh`

![[Pasted image 20231020021426.png]]

Le damos permisos a la llave RSA para que pueda leerse utilizamos el comando Chmod 600, ahora con esto vamos a revisar si podemos conectar por medio del puerto ssh con la llave que encontramos

![[Pasted image 20231020143706.png]]

Dado que la llave que nos dan está un poco desactualizada a las ultimas versiones de ssh pues colocamos el siguiente comando para poder tener una compatibilidad
Dado a que entra al sistema y con la misma se cierra pues buscamos un exploit para el ssh y poder ingresar

![[Pasted image 20231020170402.png]]
una vez entramos podemos ver la identificación, la ubicacion actual del directorio, ejecutamos el siguiente comando en python para poder trabajar mejor con la shell y ahora es investigar que podemos encontrar en las carpetas

![[Pasted image 20231020171850.png]]
encontramos muchos directorios para echar un vistazo, por ahora vamos con door2 y veremos que encontramos
 
![[Pasted image 20231020172206.png]]
dentro de la carpeta door2 encontramos un archivo llamado r00t 

![[Pasted image 20231021151810.png]]
aquí comenzó a costarme un poco más de tiempo dado que al ejecutar el directorio r00t este me da un tiempo estimado de 2 minutos y después se va a otro directorio así que tengo que buscar la manera de que el archivo permanezca por más tiempo para poder acceder.
Vamos a hacer un payload y lo guardaremos para hacer un buffer en el archivo r00t 

![[Pasted image 20231021173247.png]]

Tenemos tres carpetas llamadas Door 1 ,2 ,3 una de los archivos r00t tiene un tamaño diferente a los otros, asi que este será al que utilizaremos el payload, una vez utilizamos el archivo tenemos valor en el envio
![[Pasted image 20231021173548.png]]

Lo pasamos a /usr/share/metasploit-framework/tools/pattern_offset.rb, obtenemos un desplazamiento de 268. Entonces necesitamos escribir 268 caracteres y luego escribir la dirección de las instrucciones que queremos que se ejecuten.
Con esto tenemos privilegios Root y encontramos la flag llamada Proof.txt y con esto concluye la maquina virtual Troll2 

![[Pasted image 20231025174504.png]]
