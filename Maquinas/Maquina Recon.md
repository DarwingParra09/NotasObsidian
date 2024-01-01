Comenzando con el escaneo en la red encuentra la IP de la maquina a atacar

![[Pasted image 20231222102306.png]]

Como ya tenia guardado el escaneo de nmap para buscar puertos abiertos lo único que tuve que hacer era buscar el archivo y abrirlo,solo dos puertos fueron encontrados : 22 SSH y el 80 HTTP

![[Pasted image 20231222103012.png]]

Una vez identificado que tenemos pagina web opté por buscar que otras rutas habían dentro de la misma dirección y pude encontrar que estamos trabajando desde una pagina hecha en wordpress 

![[Pasted image 20231224160034.png]]

Este seria el index de la pagina, aunque busqué en el codigo fuente no encontré algo raro que pudiera servirme más adelante, lo unico relevante es el nombre que aparece de un usuario *RECON*, seguí investigando y encontré dentro de las rutas el admin de wordpress 

![[Pasted image 20231226193313.png]]

![[Pasted image 20231224161041.png]]

Con el comando wpscan pude encontrar más informacion detallada de la pagina, plugins, themes y usuarios, esto me dió una ventaja dado que teniendo los nombres de usuarios que han sido participes de la pagina puede con una ataque de fuerza bruta dar con la contraseña y acceder al administrador

![[Pasted image 20231227040851.png]]
![[Pasted image 20231227041201.png]]

Ya una vez identifiqué los usuarios dentro de la pagina de wordpress pues los guardé en un archivo llamado user.txt, este archivo lo voy a utilizar más adelante

![[Pasted image 20231227041859.png]]
![[Pasted image 20231227041829.png]]

Con el mismo comando wpscan doy como objetivo la direccion de la pagina, la flag -U indica que hay una lista de nombres por ello lo siguiente a escribir es el archivo llamada usuarios, la flag -P es la lista para las contraseñas, pero dado a que yo no he creado ninguno archivo de diccionario de contraseñas, pues voy a trabajar con los diccionarios predeterminados
En la siguiente imagen nos muestra el nombre que teniamos en la lista user y la contraseña de este usuario

![[Pasted image 20231227125455.png]]
![[Pasted image 20231227131409.png]]

![[Pasted image 20231227140728.png]]

vemos las especificaciones que nos pide para poder subir un archivo dentro del wordpress, lo que buscamos ahora es hacer una reverse-shell en php y obtener el acceso

![[Pasted image 20231227132915.png]]

Una vez creada la shell reverse, creamos un index.html si más, guardamos todo en un archivo .zip y lo cargamos en la pagina

![[Pasted image 20231227132843.png]]

![[Pasted image 20231227141406.png]]

Una vez montado el archivos nos vamos a la ruta upload, pero en vez de colocar index.html, colocamos el nombre de la shell reverse, no sin antes colocarnos en escucha para a la hora de entrar en la ruta esta sea ejecutada 

![[Pasted image 20231227153813.png]]
![[Pasted image 20231227153854.png]]

una vez accedemos a la carpeta offensivehack encontramos la primera flag, que nos dice que esto todavia no acaba y que debemos encontrar la flag del root

![[Pasted image 20231227155317.png]]

escribimos sudo -l para ver que comando necesitamos para subir al usuario root, en esta ocasion el comando a buscar es uno que trae gdb, escribo el comando que encontré en la web y me ayuda a identificar que no estamos todavia en el usuario root pero que si hay un usuario en docker

![[Pasted image 20231227160329.png]]
Escalamos el privilegio con docker y obtenemos la flag de root
![[Pasted image 20231227162019.png]]
![[Pasted image 20231227162049.png]]
 
