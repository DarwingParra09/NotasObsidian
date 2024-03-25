La falsificación de solicitudes del lado del servidor es una vulnerabilidad de seguridad web que permite a un atacante hacer que la aplicación del lado del servidor realice solicitudes a una ubicación no deseada.

En un ataque SSRF típico, el atacante podría hacer que el servidor establezca una conexión con servicios exclusivamente internos dentro de la infraestructura de la organización. En otros casos, pueden obligar al servidor a conectarse a sistemas externos arbitrarios. Esto podría filtrar datos confidenciales, como credenciales de autorización

## Ataques SSRF contra el servidor

En un ataque SSRF contra el servidor, el atacante hace que la aplicación realice una solicitud HTTP al servidor que aloja la aplicación, a través de su interfaz de red loopback. Por lo general, esto implica proporcionar una URL con un nombre de host como `127.0.0.1`(una dirección IP reservada que apunta al adaptador de bucle invertido) o `localhost`(un nombre de uso común para el mismo adaptador).

Por ejemplo, imagine una aplicación de compras que permite al usuario ver si un artículo está disponible en una tienda en particular. Para proporcionar información sobre las acciones, la aplicación debe consultar varias API REST de back-end. Para ello, pasa la URL al punto final API de back-end correspondiente a través de una solicitud HTTP de front-end. Cuando un usuario ve el estado del stock de un artículo, su navegador realiza la siguiente solicitud:

```
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1`
```

Esto hace que el servidor realice una solicitud a la URL especificada, recupere el estado del stock y lo devuelva al usuario.

En este ejemplo, un atacante puede modificar la solicitud para especificar una URL local al servidor:

```
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 
stockApi=http://localhost/admin
```

El servidor recupera el contenido de la `/admin`URL y se lo devuelve al usuario.

Un atacante puede visitar la `/admin`URL, pero normalmente solo los usuarios autenticados pueden acceder a la funcionalidad administrativa. Esto significa que un atacante no verá nada de interés. Sin embargo, si la solicitud a la `/admin`URL proviene de la máquina local, se omiten los controles de acceso normales. La aplicación otorga acceso completo a la funcionalidad administrativa, porque la solicitud parece originarse en una ubicación confiable.

## Ataques SSRF contra el servidor - Continuación

¿Por qué las aplicaciones se comportan de esta manera y confían implícitamente en las solicitudes que provienen de la máquina local? Esto puede surgir por varias razones:

- La verificación de control de acceso podría implementarse en un componente diferente que se encuentra frente al servidor de aplicaciones. Cuando se vuelve a establecer una conexión con el servidor, se omite la verificación.
- Para fines de recuperación ante desastres, la aplicación puede permitir el acceso administrativo sin iniciar sesión a cualquier usuario que provenga de la máquina local. Esto proporciona una manera para que un administrador recupere el sistema si pierde sus credenciales. Esto supone que sólo un usuario de plena confianza procedería directamente del servidor.
- La interfaz administrativa puede escuchar en un número de puerto diferente al de la aplicación principal y es posible que los usuarios no puedan acceder a ella directamente.

Este tipo de relaciones de confianza, donde las solicitudes que se originan en la máquina local se manejan de manera diferente a las solicitudes ordinarias, a menudo convierten a SSRF en una vulnerabilidad crítica.

# Caso 9: SSRF básico contra el servidor local

Este laboratorio tiene una función de verificación de existencias que obtiene datos de un sistema interno.

Para resolver el laboratorio, cambie la URL de verificación de existencias para acceder a la interfaz de administración `http://localhost/admin`y elimine el usuario `carlos`

Empezamos ingresando a la pagina y nos dirigimos a la URL con la ruta /admin, esta nos muestra que la interfaz solo está disponible si solo nos hemos iniciado sesión como administrador o si hemos hecho una petición en forma de bucle invertido (Loopback)

![[Pasted image 20240322233133.png]]

Volvemos a nuestra pagina de inicio y nos dirigimos a cualquier articulo posteado en la pagina, despues de la descripcion tenemos un Check Stock (validar existencias), para este momento el burpsuite de ya estar operando

![[Pasted image 20240322233406.png]]

Una vez hecho la peticion tenemos lo siguiente, a lo que debemos prestarte atención es al StockAPI ya que con esté haremos el bucle invertido, no sin antes tomar esta petición y enviarla al intruder para hacer pruebas

![[Pasted image 20240322233525.png]]

Ya con la petición en el intruder haremos una modificación en la dirección del stockAPI y veremos que la respuesta es exitosa y nos muestra por medio de etiquetas HTML que estamos ahora navegando como administrador

![[Pasted image 20240322233629.png]]
![[Pasted image 20240322233659.png]]

Aqui no acaba todo, ya una vez nosotros haber ingresado como administrador por medio del intruder, podemos ver que dentro de las etiquetas HTML está el usuario que se nos pide eliminar y nos muestra la forma correcta de redireccionar la eliminación del usuario

![[Pasted image 20240322233735.png]]

Esto lo editamos en la petición original que estamos trabajando y por consecuencia tenemos la eliminación del usuario Carlos

![[Pasted image 20240322233806.png]]

## Ataques SSRF contra otros sistemas back-end

En algunos casos, el servidor de aplicaciones puede interactuar con sistemas back-end a los que los usuarios no pueden acceder directamente. Estos sistemas suelen tener direcciones IP privadas no enrutables. Los sistemas back-end normalmente están protegidos por la topología de la red, por lo que a menudo tienen una postura de seguridad más débil. En muchos casos, los sistemas internos de back-end contienen funciones confidenciales a las que puede acceder sin autenticación cualquier persona que pueda interactuar con los sistemas.

En el ejemplo anterior, imagine que hay una interfaz administrativa en la URL de back-end `https://192.168.0.68/admin`. Un atacante puede enviar la siguiente solicitud para explotar la vulnerabilidad SSRF y acceder a la interfaz administrativa:

```
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 
stockApi=http://192.168.0.68/admin`
```

# Caso 10: SSRF básico frente a otro sistema back-end

Este laboratorio tiene una función de verificación de existencias que obtiene datos de un sistema interno.

Para resolver la práctica de laboratorio, use la función de verificación de existencias para escanear el `192.168.0.X`rango interno en busca de una interfaz de administración en el puerto 8080 y luego úsela para eliminar el usuario `carlos`.

En este punto estamos viendo la validación de las existencias de un producto y con ello encontramos un stockAPI, este será usado para verificar la interfaz de administración de la pagina

![[Pasted image 20240323021324.png]]

Haciendo ciertos cambios se modifica y enviamos al intruder, dejamos fijo el 1 para dentro de este hacer un ataque numerico que vaya desde el 1 hasta el 255

![[Pasted image 20240323021403.png]]
![[Pasted image 20240323021501.png]]

Ya una vez encontrada la dirección hacemos el cambio en el repetidor y vemos cual es la respuesta que nos redirige la nueva URL

![[Pasted image 20240323021227.png]]

Dentro de la respuesta encontramos que tenemos acceso al admin y con ello encontramos los usuarios y sus redirecciones para poder eliminar al usuario Carlos.

![[Pasted image 20240323021253.png]]

Se confirma la ruta para eliminar el usuario y ese enlace se modifica en la petición original y con esto ya hemos eliminado el usuario.

![[Pasted image 20240323021541.png]]
