## 1. Recopilación de información 

Para iniciar con este maquina debo primero escanear las direcciones ip que lleguen a ser encontradas dentro de mi red, ya sea con la herramienta *netdiscover* o *nmap* 

![[Pasted image 20240119112053.png]]

Dentro de las redes de nos muestra la herramienta podemos ver que nuestra maquina es la 192.168.1.231, utilizando la herramienta *nmap* podemos ver más información de esta red

![[Pasted image 20240119112829.png]]

Haciendo el escaneo con nmap puedo ver que tengo 3 puertos que están en servicio en este momento: 
Puerto 22/ssh, 80/http ,3306/mysql , con esta información puedo comenzar a darme una idea hacia donde poder investigar, por el momento viendo que tengo un puerto 80 es entender que hay una pagina de web trabajando con este dirección ip, así que investigaré un poco para hacer pistas sobre esta maquina.

![[Pasted image 20240119114259.png]]

A simple vista tenemos la pagina web pero solo nos da como información el servidor que se está trabajando.
Utilizando la herramienta *Gobuster* y *Dirb* nos manda una sola ruta 192.168.1.231/info.php, esta ruta muestra la informacion del servidor y demás cosas que se trabajan en la pagina.

![[Pasted image 20240119123416.png]]
![[Pasted image 20240119123505.png]]
![[Pasted image 20240119123058.png]]

Viendo que no hay mucha información dentro de la pagina web pues comienzo a buscar en los otros puertos... puede que haya otras pistas.
## 2. Análisis de vulnerabilidades

Utilizo la herramienta metasploit para hacer un ataque de fuerza bruta y lograr encontrar una contraseña que me ayude a ingresar al MySql 

![[Pasted image 20240119133430.png]]

despues de correr el ataque nos encuentra el nombre de usuario root con una contraseña que parece ser está vacio, en pocas palabras es solo dar *ENTER* y accedemos, asi que voy a ingresar a Mysql y confirmar la información.

![[Pasted image 20240119133714.png]]

Busco dentro de la base de datos y encuentro un dato especifico de un usuario por el puerto ssh, asi que con este nombre de usuario y contraseña es posible ingresar al sistema

![[Pasted image 20240119151731.png]]

## 3. Explotación

Con el nombre de usuario y la contraseña encontrada en la base de datos de mysql pude conectarme al servicio ssh, lo siguiente será investigar que puede haber dentro y en lo posible escalar privilegios

![[Pasted image 20240119190536.png]]

Mientras estoy en las carpetas de usuario encuentro un archivo ejecutable, lo examino y encuentro los siguiente 

![[Pasted image 20240119191345.png]]

Quise utilizar la ayuda de Chatgpt para para saber de que va el codigo y me dijo esto: 

Este script actúa como un registrador simple que toma líneas de la entrada estándar y las añade a un archivo de registro mientras gestiona un semáforo para evitar conflictos en la escritura del archivo. Sin embargo, ten en cuenta las variables no definidas y asegúrate de que se ajuste a tus necesidades específicas y contexto. Además, ten en cuenta que el bucle infinito puede requerir intervención externa (por ejemplo, mediante un comando de interrupción) para detener su ejecución.

Investigué un poco más y encontré de un comando llamado *crontab*, su uso es administrar las tablas de tiempo cron. Cron es un servicio que permite a los usuarios programar tareas periódicas o scripts que se ejecuten automáticamente en un intervalo de tiempo específico. El comando `crontab` permite a los usuarios:

1. **Ver las tareas cron existentes:** Puedes usar `crontab -l` para ver la lista de tareas programadas para tu usuario.
    
    `crontab -l`
    
2. **Editar las tareas cron existentes:** Puedes utilizar `crontab -e` para abrir un editor de texto y modificar las tareas cron.

    
    `crontab -e`
    
3. **Eliminar todas las tareas cron existentes:** Puedes usar `crontab -r` para eliminar todas las tareas cron programadas para tu usuario.

    
    `crontab -r`
    
4. **Instalar tareas cron desde un archivo:** Puedes usar `crontab archivo` para leer tareas cron desde un archivo y programarlas.

    
    `crontab mi_archivo_cron.txt`
    

Cada línea en un archivo cron o en la salida de `crontab -l` sigue un formato específico. Un ejemplo de formato de línea cron sería:
	
	`# m h dom mon dow command 0 2 * * * /ruta/al/script.sh`
	
Esto ejecutaría `/ruta/al/script.sh` todos los días a las 2:00 AM.

Busco el archivo crontab para saber si este archivo .sh estaba siendo ejecutado cada cierto tiempo y logro ver que por medio del usuario root se ejecuta tal archivo

![[Pasted image 20240119194016.png]]

Pienso editar el archivo y pasar una shell reverse y entrar al sistema root

![[Pasted image 20240119202346.png]]
## 4. Post-Explotación

Entro en modo de escucha antes de guardar el archivo para que una vez se ejecute puede ser detectado por mi netcat local

![[Pasted image 20240119200305.png]]

Ya detectado la shell inversa busco identificar si verdaderamente estamos en el usuario root y pues efectivamente si, lo ultimo por hacer es buscar la flag y con esto terminamos la maquina

![[Pasted image 20240119202129.png]]
