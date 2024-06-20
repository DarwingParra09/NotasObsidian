
# Enumeración de nombres de usuario a través de diferentes respuestas

Este laboratorio es vulnerable a ataques de fuerza bruta de enumeración de nombres de usuario y contraseñas. Tiene una cuenta con un nombre de usuario y contraseña predecibles, que se pueden encontrar en las siguientes listas de palabras:

- [Nombres de usuario de los candidatos](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Contraseñas de candidatos](https://portswigger.net/web-security/authentication/auth-lab-passwords)

Para resolver la práctica de laboratorio, enumere un nombre de usuario válido, aplique fuerza bruta a la contraseña de este usuario y luego acceda a su página de cuenta.

Al iniciar sesión colocamos cualquier credencial con la intención que sea detectada por el Burpsuite, enviamos esta petición al intruder y marcamos el usuario el cual será modificado para hacer el ataque de nombres

![[Pasted image 20240320013101.png]]

Dentro del ataque que hacemos con la lista de nombres podemos ver que hay un tamaño diferente, este tamaño nos confirma el usuario correcto dentro del login

![[Pasted image 20240320013014.png]]

Ahora hacemos el mismo método pero con la contraseña, una vez ya encontrado podemos cambiar los credenciales y tener acceso a la cuenta

![[Pasted image 20240320013214.png]]

![[Pasted image 20240320020130.png]]

Ya con la contraseña ingresada podemos ver el nombre de nuestro usuario

![[Pasted image 20240320020409.png]]

# Bypass simple 2FA

Se puede omitir la autenticación de dos factores de este laboratorio. Ya obtuvo un nombre de usuario y una contraseña válidos, pero no tiene acceso al código de verificación 2FA del usuario. Para resolver el laboratorio accede a la página de la cuenta de Carlos.

- Tus credenciales:`wiener:peter`
- Credenciales de la víctima`carlos:montoya`

Al haber ingresado por medio del usuario wiener tenemos la siguiente entrada que es para terminar de verificar la entrada a la cuenta, buscamos el email del cliente y nos da el código de seguridad a ingresar

![[Pasted image 20240321143032.png]]

![[Pasted image 20240321143156.png]]

Ya despues de haber ingresado el codigo de seguridad me redirecciona a la cuenta principal

![[Pasted image 20240321143326.png]]

Haremos el inicio de sesión y cuando nos redirija a la pagina para digitar el codigo de seguridad puedes editamos el URL 

![[Pasted image 20240321143511.png]]
![[Pasted image 20240321143546.png]]

Al modificar la dirección vemos que pudimos evadir el doble factor y hemos ingresado directamente a la cuenta

![[Pasted image 20240321143601.png]]

# Lógica rota de restablecimiento de contraseña

La funcionalidad de restablecimiento de contraseña de este laboratorio es vulnerable. Para resolver la práctica de laboratorio, restablezca la contraseña de Carlos, luego inicie sesión y acceda a su página "Mi cuenta".

- Tus credenciales:`wiener:peter`
- Nombre de usuario de la víctima:`carlos`

Para este laboratorio tenemos las credenciales de wiener y debemos acceder a la cuenta de nuestra victima Carlos, como no tenemos la contraseña nos dirigimos a olvidamos nuestra contraseña

![[Pasted image 20240620164027.png]]

Tenemos el email dentro de la cuenta de wiener, dado que a la hora de darle olvidar contraseña se nos envia un caja de texto donde debemos dar un usuario o email que nos enviará un manera de recuperar la cuenta

![[Pasted image 20240620164156.png]]
![[Pasted image 20240620164557.png]]
![[Pasted image 20240620164616.png]]
![[Pasted image 20240620164817.png]]

Para registrar una nueva contraseña debemos confirmarla por segunda vez y vemos que por el hecho de tener el correo de wiener pues la contraseña cambiaria para ese usuario, si editamos y colocamos el nombre de carlos terminamos modificando esta petición y obtendríamos una nueva contraseña para el usuario

![[Pasted image 20240620164946.png]]
![[Pasted image 20240620165017.png]]
![[Pasted image 20240620165037.png]]
![[Pasted image 20240620165107.png]]
