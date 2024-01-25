## 1. Recolección de Información

Utilizo el comando *Netdiscover*  para identificar la dirección IP

![[Pasted image 20240124154831.png]]

Una vez ya identificada la red hago el escaneo de puertos con Nmap para ver que servicios pueden estarse ejecutando.  

![[Pasted image 20240124160538.png]]

Veo que tengo una pagina y comienzo a hacer la enumeración de directorios para encontrar pistas e ir ingresando en cada una de estas rutas

![[Pasted image 20240124162734.png]]

Entro a la dirección de la pagina y veo que no se encuentra nada dentro del servidor, no hay información y comienzo a ver las otras rutas

![[Pasted image 20240124165704.png]]

Dentro de la ruta Robots.txt me avisan que las rutas que especifican están deshabilitadas, pero fijándome en el escaneo con gobuster me dice que hay archivos html por cada una de estas rutas, asi que no pierdo nada con investigar y ver si es cierto lo que se me está informando en el archivo robots. En la misma ruta hay un enunciado que parece estar en base64 asi que voy a decodificarlo y ver de que trata.

![[Pasted image 20240124165922.png]]

Una vez decodificado el mensaje me da como resultado lo siguiente `ssh-bruteforce-sudoit`, esto puedo tomarlo como una pista hacia donde debería dirigirme y hacer un ataque de fuerza bruta al puerto 22/ssh. Pero antes de eso seguiré buscando que puede haber en las otras rutas

![[Pasted image 20240124173412.png]]

La ruta /sudo.html me presenta el siguiente mensaje y por costumbre indago en código fuente porque puede darse algún comentario que sea de utilidad

![[Pasted image 20240124173810.png]]
![[Pasted image 20240124174230.png]]

Ahora ya con este nombre pues lo guardo, puede que me sea útil dentro del ataque de fuerza bruta, mientras sigo investigando.

Dentro de la otra ruta hay un mensaje en tipo pregunta, vuelvo nuevamente a buscar información por el código fuente y encuentro ciertos datos.

![[Pasted image 20240124175641.png]]
![[Pasted image 20240124175706.png]]
## 2. Análisis de vulnerabilidades

Utilicé dos herramientas para fuerza bruta y me da la misma respuesta con su contraseña, así que lo que viene a continuación es ingresar por medio de ssh  

![[Pasted image 20240124185133.png]]
![[Pasted image 20240124185159.png]]
## 3. Explotación

![[Pasted image 20240124191205.png]]

La ruta /var aloja un archivo zip, lo envío a 

![[Pasted image 20240124194501.png]]

Copio el archivo zip a mi directorio local, trato de encontrar la contraseña y descomprimirlo. Al descomprimirlo encuentro un archivo pero este no tenia informacion

![[Pasted image 20240124194439.png]]
![[Pasted image 20240124201628.png]]
![[Pasted image 20240124202449.png]]
![[Pasted image 20240124202840.png]]

Despues dentro de otra ruta encuentra un mensaje codificado, la desencripto y pruebo para acceder a root ; pero tampoco sirve.

![[Pasted image 20240124203337.png]]
![[Pasted image 20240124203405.png]]

## 4. Post-Explotación

Ingreso a .bash_history y encuentro una vulnerabilidad la cual hace que se acceda al usuario root, tengo la sesión, aquí miré en la carpeta root pero no hay flag, encontré el hostname llamado CTF

![[Pasted image 20240124204509.png]]
![[Pasted image 20240124211009.png]]
![[Pasted image 20240124211208.png]]
