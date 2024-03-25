## Que es un control de acceso? 

El control de acceso es la aplicación de restricciones sobre quién o qué está autorizado a realizar acciones o acceder a recursos. En el contexto de las aplicaciones web, el control de acceso depende de la autenticación y la gestión de sesiones:

- **La autenticación** confirma que el usuario es quien dice ser.
- **La gestión de sesiones** identifica qué solicitudes HTTP posteriores realiza ese mismo usuario.
- **El control de acceso** determina si el usuario puede realizar la acción que intenta realizar.

Los controles de acceso rotos son comunes y a menudo presentan una vulnerabilidad de seguridad crítica. El diseño y la gestión de controles de acceso es un problema complejo y dinámico que aplica restricciones comerciales, organizativas y legales a una implementación técnica. Las decisiones de diseño del control de acceso deben ser tomadas por humanos, por lo que el potencial de errores es alto.

## Funcionalidad desprotegida

En su forma más básica, la escalada de privilegios verticales surge cuando una aplicación no aplica ninguna protección para funciones confidenciales. Por ejemplo, las funciones administrativas pueden estar vinculadas desde la página de bienvenida de un administrador pero no desde la página de bienvenida de un usuario. Sin embargo, un usuario podría acceder a las funciones administrativas navegando hasta la URL de administrador correspondiente.

Por ejemplo, un sitio web puede alojar funciones confidenciales en la siguiente URL:

`https://insecure-website.com/admin`

Cualquier usuario puede acceder a esto, no solo los usuarios administrativos que tienen un enlace a la funcionalidad en su interfaz de usuario. En algunos casos, la URL administrativa puede revelarse en otras ubicaciones, como el `robots.txt`archivo:

`https://insecure-website.com/robots.txt`

Incluso si la URL no se divulga en ninguna parte, un atacante puede usar una lista de palabras para forzar la ubicación de la funcionalidad confidencial.

# Caso 2: funcionalidad de administración desprotegida 

![[Pasted image 20240317232600.png]]

En esta ocasión tenemos un login y el objetivo es eliminar el usuario Carlos, aunque no sabemos ni el usuario ni la contraseña podemos llegar a buscar más información por medio del archivo de texto **Robots.txt**

![[Pasted image 20240317233120.png]]

Encuentro dentro de este una ruta y la copio en la URL, encuentro dos usuario y entre estos está carlos, lo elimino y ya tenemos el reto finalizado. No siempre el archivo Robots nos puede traer información pero en esta ocasión era un punto clave

![[Pasted image 20240317233152.png]]

## Continuación de funcionalidad desprotegida

En algunos casos, la funcionalidad confidencial se oculta dándole una URL menos predecible. Este es un ejemplo de la llamada "seguridad por oscuridad". Sin embargo, ocultar funciones confidenciales no proporciona un control de acceso efectivo porque los usuarios pueden descubrir la URL ofuscada de varias maneras.

Imagine una aplicación que aloja funciones administrativas en la siguiente URL:

`https://insecure-website.com/administrator-panel-yb556`

Es posible que un atacante no pueda adivinar esto directamente. Sin embargo, es posible que la aplicación aún filtre la URL a los usuarios. La URL puede divulgarse en JavaScript que construye la interfaz de usuario según la función del usuario:
```
<script> 
	var isAdmin = false; 
	if (isAdmin) { ... 
		var adminPanelTag = document.createElement('a'); 
		adminPanelTag.setAttribute('https://insecure-website.com/administrator-panel-yb556');         adminPanelTag.innerText = 'Admin panel';
	 ... } 
</script>
```

Este script agrega un enlace a la interfaz de usuario del usuario si es un usuario administrador. Sin embargo, el script que contiene la URL es visible para todos los usuarios independientemente de su función.

# Caso 3: funcionalidad de administración desprotegida con URL impredecible

Este laboratorio tiene un panel de administración desprotegido. Está ubicado en una ubicación impredecible, pero la ubicación se revela en algún lugar de la aplicación.
Resuelva la práctica de laboratorio accediendo al panel de administración y utilizándolo para eliminar el usuario `carlos`.

Para esta ocasión tenemos nuestra pagina habitual, pero esta vez no tenemos acceso de la ruta Robots.txt, pero no es la única información para encontrar datos, regularmente cuando se accede a una pagina web buscamos en su código de fuente comentarios o código que hayan dejado a la vista.

![[Pasted image 20240318001133.png]]

Con lo antes expuesto me voy a la sesión de cuenta y miro el código fuente, encuentro una ruta dentro de un script y me dirijo hacia ella

![[Pasted image 20240318001222.png]]

![[Pasted image 20240318001308.png]]

La ruta la tengo tachada con color amarrillo, esta ruta la paso a la URL y me redirecciona hacia los usuarios que están registrados dentro de la pagina, con esto elimino al usuario carlos y se da por terminado el reto

![[Pasted image 20240318001433.png]]

##  Métodos de control de acceso basados ​​en parámetros

Algunas aplicaciones determinan los derechos de acceso o la función del usuario al iniciar sesión y luego almacenan esta información en una ubicación controlable por el usuario. Esto podría ser:

- Un campo escondido.
- Una Cookies.
- Un parámetro de cadena de consulta preestablecido.

La aplicación toma decisiones de control de acceso en función del valor enviado. Por ejemplo:

`https://insecure-website.com/login/home.jsp?admin=true https://insecure-website.com/login/home.jsp?role=1`

Este enfoque es inseguro porque un usuario puede modificar el valor y acceder a funciones para las que no está autorizado, como funciones administrativas.

# Caso 4: rol de usuario controlado por el parámetro de solicitud

Este laboratorio tiene un panel de administración en `/admin`, que identifica a los administradores que utilizan una cookie falsificable.

Resuelva el laboratorio accediendo al panel de administración y usándolo para eliminar al usuario `carlos`

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240318123440.png]]

Al ingresar los datos podemos iniciar sesión, pero en esta ocasión Burpsuite no muestra que dentro de las cookies tenemos un derecho de acceso, esto nos muestra que el usuario al que estamos ingresando no es Administrador, pero teniendo esto a la vista podemos editarlo y hacerlo pasar como administrador

![[Pasted image 20240318123544.png]]
![[Pasted image 20240318123907.png]]

Esto nos dará una nueva opción que hacia el panel de usuarios y podemos borrar el nombre Carlos

![[Pasted image 20240318123845.png]]
![[Pasted image 20240318123653.png]]

## Escalada de privilegios horizontales

La escalada de privilegios horizontal se produce si un usuario puede obtener acceso a recursos que pertenecen a otro usuario, en lugar de a sus propios recursos de ese tipo. Por ejemplo, si un empleado puede acceder a los registros de otros empleados además de a los suyos propios, entonces se trata de una escalada de privilegios horizontal.

Los ataques de escalada de privilegios horizontales pueden utilizar tipos de métodos de explotación similares a los de la escalada de privilegios vertical. Por ejemplo, un usuario podría acceder a la página de su propia cuenta utilizando la siguiente URL:

`https://insecure-website.com/myaccount?id=123`

Si un atacante modifica el `id`valor del parámetro por el de otro usuario, podría obtener acceso a la página de la cuenta de otro usuario y a los datos y funciones asociados.

```
Este es un ejemplo de una vulnerabilidad de referencia directa a objetos (IDOR) insegura. Este tipo de vulnerabilidad surge cuando los valores de los parámetros del controlador de usuario se utilizan para acceder a recursos o funciones directamente.
```

En algunas aplicaciones, el parámetro explotable no tiene un valor predecible. Por ejemplo, en lugar de un número creciente, una aplicación podría utilizar identificadores únicos globales (GUID) para identificar a los usuarios. Esto puede impedir que un atacante adivine o prediga el identificador de otro usuario. Sin embargo, los GUID que pertenecen a otros usuarios pueden revelarse en otras partes de la aplicación donde se hace referencia a los usuarios, como mensajes o reseñas de usuarios.

# Caso 5: ID de usuario controlado por parámetro de solicitud, con ID de usuario impredecibles

Esta práctica de laboratorio tiene una vulnerabilidad de escalada de privilegios horizontal en la página de la cuenta de usuario, pero identifica a los usuarios con GUID.

Para resolver la práctica de laboratorio, busque el GUID de `carlos`y luego envíe su clave API como solución.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240319021550.png]]

Una vez iniciada la sesion del usuario wiener en la URL encontramos la identificación del usuario, este identificador a la vista podría ser cambiado por otro usuario, asi que para nuestro reto el objetivo es conseguir el acceso al usuario **carlos**

![[Pasted image 20240319021849.png]]

Dentro de la pagina encontramos algunos post con su editor y vemos que carlos tiene una publicación expuesta en la web, al ingresar en el nombre nos muestra su identificador, este ID lo puedo anotar o guardar en otro lado

![[Pasted image 20240319021647.png]]
![[Pasted image 20240319021717.png]]

Teniendo en cuenta que seguimos dentro de la sesión de wiener nos dirigimos a la cuenta y el Burpsuite nos manda la petición hay donde nos vamos a dirigir, lo que haremos es modificar el ID con el de carlos y enviar la petición, una vez esto se ejecuta deja de mostrarnos como usuario wiener y nos muestra como usuario Carlos con su API Key

![[Pasted image 20240319022309.png]]
![[Pasted image 20240319022133.png]]

## Escalada de privilegios horizontal a vertical

A menudo, un ataque de escalada de privilegios horizontal puede convertirse en una escalada de privilegios vertical, al comprometer a un usuario con más privilegios. Por ejemplo, una escalada horizontal podría permitir a un atacante restablecer o capturar la contraseña de otro usuario. Si el atacante apunta a un usuario administrativo y compromete su cuenta, entonces puede obtener acceso administrativo y así realizar una escalada de privilegios vertical.

Un atacante podría obtener acceso a la página de la cuenta de otro usuario utilizando la técnica de manipulación de parámetros ya descrita para la escalada de privilegios horizontal:

`https://insecure-website.com/myaccount?id=456`

Si el usuario objetivo es un administrador de aplicaciones, el atacante obtendrá acceso a una página de cuenta administrativa. Esta página puede revelar la contraseña del administrador o proporcionar un medio para cambiarla, o puede proporcionar acceso directo a una funcionalidad privilegiada.

# Caso 6: ID de usuario controlado por parámetro de solicitud con divulgación de contraseña

Esta práctica de laboratorio tiene una página de cuenta de usuario que contiene la contraseña existente del usuario actual, precompletada en una entrada enmascarada.

Para resolver la práctica de laboratorio, recupere la contraseña del administrador y luego úsela para eliminar el usuario `carlos`.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240319021550.png]]

Con la ayuda de Burpsuite tenemos la petición de inicio de sesión de wiener, abrimos una ventana en el repetidor de burpsuite y cambiamos el ID y colocamos administrador, lo siguiente es mandar la petición y en respuesta tenemos la contraseña de ingreso del usuario administrador 

![[Pasted image 20240319165553.png]]
![[Pasted image 20240319165611.png]]

Teniendo ya las credenciales especificas entramos al usario administrador y podemos ir a la opcion de panel de administracion y eliminar la cuenta de usuario Carlos

![[Pasted image 20240319165733.png]]
![[Pasted image 20240319165758.png]]
![[Pasted image 20240319165821.png]]


