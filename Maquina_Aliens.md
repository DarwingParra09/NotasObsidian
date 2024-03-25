## 1. Recopilación de Información

Con el uso de Netdiscover obtengo la red la cual voy a atacar

![[Pasted image 20240222010924.png]]

Ahora sabiendo que nuestra maquina a auditar es la 192.168.1.248 haré un escaneo de puertos con Nmap

```
sudo nmap 192.168.1.248 -sSV -p- --min-rate 5000 -A 
```

![[Pasted image 20240222015731.png]]

Como se puede observar tengo 3 puertos en ejecución, por ahora averiguaré que trae el puerto 80 y con base en este haré fuzzing para saber que otras rutas están cargadas en esta misma dirección

![[Pasted image 20240222020846.png]]

Mientras hacia el escaneo de rutas no encontré nada llamativo 

