## 1. Recolección de Información

Con el comando *Netdiscover* buscamos la dirección IP a la que vamos a hacer el ataque

![[Pasted image 20240129151732.png]]

Una vez identificada la red hago un escaneo de puertos con *Nmap* para saber que servicios se están ejecutando

![[Pasted image 20240129165749.png]]

Puedo ver que hay dos puertos activos en estos momentos, me interesa por ahora el puerto 80, así que utilizando la ip que hace un momento tomé da la maquina indago en la pagina web, no sin antes hacer una enumeración de rutas con *gobuster*

![[Pasted image 20240129165550.png]]

No encontré mas información sobre las rutas. comienzo a indagar la ruta index y puedo hallar multiples usuarios con sus contraseñas, pero como de costumbre no solo me fio en lo primero que veo, asi que me dirijo al cogido fuente y encuentro una pista, puede ser un nombre u otra cosa

![[Pasted image 20240129173041.png]]
![[Pasted image 20240129173104.png]]

ROT47 no suena a un usuario, asi que investigo en google por saber si hay algo relacionado y encuentro que es un encriptador, desde un principio la pagina index tiene en la lista de usuarios y contraseñas dos cadenas de texto sin ninguna coherencia, asi que las utilizo con el ROT47 y me dan el nombre de usuario 

![[Pasted image 20240129174516.png]]

## 2. Análisis de vulnerabilidades

Guardo los datos que he encontrado y comienza a hacer un ataque de fuerza bruta con *Hydra* para el puerto ssh y obtengo un usuario y contraseña

![[Pasted image 20240129175338.png]]

Ya con estas credenciales puedo acceder al puerto ssh e indagar para elevar privilegios
## 3. Explotación

Obtenemos acceso y lo que sigue es investigar sobre posibles vulnerabilidades dentro del usuario shailendra

![[Pasted image 20240129175621.png]]

El usuario nos muestra una posible pista, la siguiente vulnerabilidad estará encaminada a docker 

![[Pasted image 20240129175849.png]]
## 4. Post-Explotación

Reviso el historial del usuario shailendra y encuentro que estuvieron corriendo una imagen, pero cuando digité `docker images` no salió nada, asi que busco correr esta imagen que estuvieron ejecutando en el historial en su momento y dentro de una pagina buscaré la manera de elevar privilegios con docker utilizando la imagen que acabo de correr

![[Pasted image 20240129181241.png]]

Busco el comando para elevar privilegios en docker y escribo el nombre de la imagen, una vez todo listo, doy enter y ya somos usuarios root, vamos a la carpeta raiz y tenemos nuestra flag. Y con eso concluye la maquina

![[Pasted image 20240129181624.png]]
![[Pasted image 20240129181738.png]]

