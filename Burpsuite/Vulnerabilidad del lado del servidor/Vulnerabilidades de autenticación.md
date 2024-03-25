Conceptualmente, las vulnerabilidades de autenticación son fáciles de entender. Sin embargo, suelen ser fundamentales debido a la clara relación entre autenticación y seguridad.

Las vulnerabilidades de autenticación pueden permitir a los atacantes obtener acceso a datos y funciones confidenciales. También exponen una superficie de ataque adicional para futuras vulnerabilidades. Por este motivo, es importante aprender cómo identificar y explotar las vulnerabilidades de autenticación y cómo eludir las medidas de protección comunes.

![[Pasted image 20240319234801.png]]

## ¿Cuál es la diferencia entre autenticación y autorización?

La autenticación es el proceso de verificar que un usuario es quien dice ser. La autorización implica verificar si un usuario tiene permiso para hacer algo.

Por ejemplo, la autenticación determina si alguien que intenta acceder a un sitio web con el nombre de usuario `Carlos123`es realmente la misma persona que creó la cuenta.

Una vez `Carlos123`autenticado, sus permisos determinan qué está autorizado a hacer. Por ejemplo, pueden estar autorizados a acceder a información personal sobre otros usuarios o realizar acciones como eliminar la cuenta de otro usuario.

## Ataques de fuerza bruta

Un ataque de fuerza bruta se produce cuando un atacante utiliza un sistema de prueba y error para adivinar las credenciales de usuario válidas. Estos ataques suelen automatizarse mediante listas de palabras de nombres de usuario y contraseñas. Automatizar este proceso, especialmente utilizando herramientas dedicadas, potencialmente permite a un atacante realizar una gran cantidad de intentos de inicio de sesión a alta velocidad.

La fuerza bruta no siempre consiste simplemente en hacer conjeturas completamente aleatorias sobre nombres de usuarios y contraseñas. Al utilizar también lógica básica o conocimiento disponible públicamente, los atacantes pueden ajustar los ataques de fuerza bruta para hacer conjeturas mucho más fundamentadas. Esto aumenta considerablemente la eficacia de este tipo de ataques. Los sitios web que dependen del inicio de sesión basado en contraseña como único método para autenticar a los usuarios pueden ser muy vulnerables si no implementan suficiente protección de fuerza bruta.

## Nombres de usuario de fuerza bruta

Los nombres de usuario son especialmente fáciles de adivinar si siguen un patrón reconocible, como una dirección de correo electrónico. Por ejemplo, es muy común ver inicios de sesión comerciales en formato `firstname.lastname@somecompany.com`. Sin embargo, incluso si no existe un patrón obvio, a veces incluso cuentas con altos privilegios se crean utilizando nombres de usuario predecibles, como `admin`o `administrator`.

Durante la auditoría, verifique si el sitio web revela públicamente posibles nombres de usuario. Por ejemplo, ¿puedes acceder a los perfiles de usuario sin iniciar sesión? Incluso si el contenido real de los perfiles está oculto, el nombre utilizado en el perfil a veces es el mismo que el nombre de usuario de inicio de sesión. También debe verificar las respuestas HTTP para ver si se divulga alguna dirección de correo electrónico. En ocasiones, las respuestas contienen direcciones de correo electrónico de usuarios con muchos privilegios, como administradores o soporte de TI.

## Contraseñas de fuerza bruta

De manera similar, las contraseñas pueden ser de fuerza bruta, y la dificultad varía según la seguridad de la contraseña. Muchos sitios web adoptan algún tipo de política de contraseñas, lo que obliga a los usuarios a crear contraseñas de alta entropía que son, al menos en teoría, más difíciles de descifrar utilizando únicamente la fuerza bruta. Por lo general, esto implica hacer cumplir las contraseñas con:

- Un número mínimo de caracteres.
- Una mezcla de letras minúsculas y mayúsculas.
- Al menos un carácter especial

Sin embargo, si bien las contraseñas de alta entropía son difíciles de descifrar solo para las computadoras, podemos utilizar un conocimiento básico del comportamiento humano para explotar las vulnerabilidades que los usuarios introducen involuntariamente en este sistema. En lugar de crear una contraseña segura con una combinación aleatoria de caracteres, los usuarios a menudo toman una contraseña que pueden recordar e intentan manipularla para que se ajuste a la política de contraseñas. Por ejemplo, si `mypassword`no está permitido, los usuarios pueden probar algo como `Mypassword1!`o `Myp4$$w0rd`en su lugar.

En los casos en que la política requiere que los usuarios cambien sus contraseñas periódicamente, también es común que los usuarios simplemente realicen cambios menores y predecibles en su contraseña preferida. Por ejemplo, `Mypassword1!`se convierte `Mypassword1?`o`Mypassword2!.`

Este conocimiento de las credenciales probables y los patrones predecibles significa que los ataques de fuerza bruta a menudo pueden ser mucho más sofisticados y, por lo tanto, efectivos, que simplemente iterar a través de cada combinación posible de personajes

## Enumeración de nombre de usuario

La enumeración de nombres de usuario ocurre cuando un atacante puede observar cambios en el comportamiento del sitio web para identificar si un nombre de usuario determinado es válido.

La enumeración de nombres de usuario normalmente ocurre en la página de inicio de sesión, por ejemplo, cuando ingresa un nombre de usuario válido pero una contraseña incorrecta, o en los formularios de registro cuando ingresa un nombre de usuario que ya está en uso. Esto reduce en gran medida el tiempo y el esfuerzo necesarios para forzar un inicio de sesión porque el atacante puede generar rápidamente una lista corta de nombres de usuario válidos.

Al intentar forzar una página de inicio de sesión por fuerza bruta, debes prestar especial atención a las diferencias en:

- **Códigos de estado** : durante un ataque de fuerza bruta, es probable que el código de estado HTTP devuelto sea el mismo para la gran mayoría de las conjeturas porque la mayoría de ellas serán incorrectas. Si una suposición devuelve un código de estado diferente, esto es una fuerte indicación de que el nombre de usuario era correcto. Es una buena práctica que los sitios web devuelvan siempre el mismo código de estado independientemente del resultado, pero esta práctica no siempre se sigue.
- **Mensajes de error** : a veces el mensaje de error devuelto es diferente dependiendo de si tanto el nombre de usuario como la contraseña son incorrectos o solo la contraseña era incorrecta. Es una buena práctica que los sitios web utilicen mensajes genéricos idénticos en ambos casos, pero a veces se producen pequeños errores tipográficos. Un solo carácter fuera de lugar hace que los dos mensajes sean distintos, incluso en los casos en que el carácter no es visible en la página representada.
- **Tiempos de respuesta** : si la mayoría de las solicitudes se manejaron con un tiempo de respuesta similar, cualquiera que se desvíe de este sugiere que algo diferente estaba sucediendo detrás de escena. Esta es otra indicación de que el nombre de usuario adivinado podría ser correcto. Por ejemplo, es posible que un sitio web solo compruebe si la contraseña es correcta si el nombre de usuario es válido. Este paso adicional podría provocar un ligero aumento en el tiempo de respuesta. Esto puede ser sutil, pero un atacante puede hacer que este retraso sea más obvio ingresando una contraseña excesivamente larga que el sitio web tarda mucho más en procesar.

# Caso 7: Enumeración de nombres de usuario a través de diferentes respuestas

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

# CASO 8: bypass simple 2FA

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
