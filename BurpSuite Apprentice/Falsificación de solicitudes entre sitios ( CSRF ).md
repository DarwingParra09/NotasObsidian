La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF.

Para resolver la práctica de laboratorio, cree algo de HTML que utilice un ataque CSRF para cambiar la dirección de correo electrónico del espectador y cárguelo en su servidor de explotación.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

CSRF funciona porque el navegador de la víctima, al estar autenticado en el sitio web objetivo, envía automáticamente las credenciales de sesión necesarias cuando realiza la solicitud maliciosa. El atacante solo necesita inducir a la víctima a visitar una página controlada por él que envíe la solicitud maliciosa.

Para esta ocasión tenemos una cuenta con la sesión iniciada, el atacante (nosotros) debemos tener cierto conocimientos de las peticiones que realizará al querer cambiar su cuenta email.

![[Pasted image 20240618084534.png]]

En ejemplo tenemos la peticion de cambio de email de la siguiente manera, lo que busca el atacante es saber como se ven las peticiones de configuraciones, transferencias de dinero y más dentro de paginas donde la victima entra a menudo

![[Pasted image 20240618085433.png]]

Creamos un PoC que tendrá lo siguiente, este HTML lo que hace es que si la victima ya ha iniciado sesión al enviarle el HTML será redirigido a su cuenta pero tendrá un cambio en su email de manera automática dado que el archivo ya está entregando la petición sin que el usuario se de cuenta 

![[Pasted image 20240618084618.png]]
![[Pasted image 20240618084512.png]]
