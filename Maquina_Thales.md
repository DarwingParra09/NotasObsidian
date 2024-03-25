## 1. Recopilación de Información

Con el uso de netdiscover puedo encontrar la ip a atacar
![[Pasted image 20240229031929.png]]

Una vez identifico la red, hago un escaneo de puertos con nmap para observar que servicios se están ejecutando

![[Pasted image 20240229034423.png]]

Ya detectada los puertos consigo ver un servidor web alojado en el puerto 8080, haré un escaneo de rutas en Gobuster y veré que otros enlaces puedo encontrar

