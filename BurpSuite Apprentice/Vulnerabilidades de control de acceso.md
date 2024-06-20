
# Funcionalidad de administración desprotegida 

![[Pasted image 20240317232600.png]]

En esta ocasión tenemos un login y el objetivo es eliminar el usuario Carlos, aunque no sabemos ni el usuario ni la contraseña podemos llegar a buscar más información por medio del archivo de texto **Robots.txt**

![[Pasted image 20240317233120.png]]

Encuentro dentro de este una ruta y la copio en la URL, encuentro dos usuario y entre estos está carlos, lo elimino y ya tenemos el reto finalizado. No siempre el archivo Robots nos puede traer información pero en esta ocasión era un punto clave

![[Pasted image 20240317233152.png]]

# Funcionalidad de administración desprotegida con URL impredecible

Este laboratorio tiene un panel de administración desprotegido. Está ubicado en una ubicación impredecible, pero la ubicación se revela en algún lugar de la aplicación.
Resuelva la práctica de laboratorio accediendo al panel de administración y utilizándolo para eliminar el usuario `carlos`.

Para esta ocasión tenemos nuestra pagina habitual, pero esta vez no tenemos acceso de la ruta Robots.txt, pero no es la única información para encontrar datos, regularmente cuando se accede a una pagina web buscamos en su código de fuente comentarios o código que hayan dejado a la vista.

![[Pasted image 20240318001133.png]]

Con lo antes expuesto me voy a la sesión de cuenta y miro el código fuente, encuentro una ruta dentro de un script y me dirijo hacia ella

![[Pasted image 20240318001222.png]]

![[Pasted image 20240318001308.png]]

La ruta la tengo tachada con color amarrillo, esta ruta la paso a la URL y me redirecciona hacia los usuarios que están registrados dentro de la pagina, con esto elimino al usuario carlos y se da por terminado el reto

![[Pasted image 20240318001433.png]]

# Rol de usuario controlado por el parámetro de solicitud

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

# ID de usuario controlado por parámetro de solicitud, con ID de usuario impredecibles

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

Caso 6: ID de usuario controlado por parámetro de solicitud con divulgación de contraseña

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

# La función del usuario se puede modificar en el perfil del usuario

Este laboratorio tiene un panel de administración en `/admin`. Solo es accesible para usuarios que hayan iniciado sesión con un `roleid`puntaje de 2.

Resuelva el laboratorio accediendo al panel de administración y usándolo para eliminar al usuario `carlos.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240620101109.png]]
![[Pasted image 20240620101232.png]]

Editamos de la petición en el JSON con un roleid:2, esto nos daría privilegios de administrador y podríamos eliminar usuarios u otras cosas que hayan dentro de la pagina

![[Pasted image 20240620101435.png]]
![[Pasted image 20240620101458.png]]
![[Pasted image 20240620101526.png]]

# ID de usuario controlado por parámetro de solicitud con fuga de datos en redirección

Esta práctica de laboratorio contiene una vulnerabilidad [de control de acceso](https://portswigger.net/web-security/access-control) donde se filtra información confidencial en el cuerpo de una respuesta de redireccionamiento.

Para resolver la práctica de laboratorio, obtenga la clave API para el usuario `carlos`y envíela como solución.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240620104811.png]]

Al cambiar el id por el usuario Carlos vemos que se da una redirección  que nos muestra la ApiKey y da con la finalidad del laboratorio

![[Pasted image 20240620104844.png]]
![[Pasted image 20240620104901.png]]

# ID de usuario controlado por parámetro de solicitud

Esta práctica de laboratorio tiene una vulnerabilidad de escalada de privilegios horizontal en la página de la cuenta del usuario.

Para resolver la práctica de laboratorio, obtenga la clave API para el usuario `carlos`y envíela como solución.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240620113020.png]]
![[Pasted image 20240620112824.png]]
![[Pasted image 20240620112804.png]]

# Referencias directas a objetos inseguros

Esta práctica de laboratorio almacena los registros de chat de los usuarios directamente en el sistema de archivos del servidor y los recupera mediante URL estáticas.

Resuelva la práctica de laboratorio buscando la contraseña del usuario `carlos`e iniciando sesión en su cuenta.

Para este laboratorio tenemos una caja de mensajes, la finalidad de este laboratorio es llegar a obtener la contraseña del usuario carlos, la opción view transcript nos descarga lo que se ha hablado durante el chat, pero nos llega el nombre del archivo como 2.txt, volvemos a intentar descargar un nuevo chat en donde nos entrega un nuevo archivo con nombre de 3.txt 

![[Pasted image 20240620152111.png]]

Cambiamos el nombre a la hora de la transcripción y pedimos que nos llegue el formulario 1.txt, al abrirlo vemos un chat en donde el usuario posiblemente carlos olvidó sus credenciales y allí mismo le confirman su contraseña. Utilizamos esta contraseña para darnos paso a la sesión de carlos y hemos completado el laboratorio

![[Pasted image 20240620152201.png]]
![[Pasted image 20240620152036.png]]
![[Pasted image 20240620151940.png]]
![[Pasted image 20240620152303.png]]
![[Pasted image 20240620152320.png]]