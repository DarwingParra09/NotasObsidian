
# XSS almacenado en contexto HTML sin nada codificado

Para resolver esta práctica de laboratorio, envíe un comentario que llame a la `alert`función cuando se vea la publicación del blog.

Este ataque consiste en un envio de script a una caja de comentario, al igual que el ataque anterior nos devuelve el resultado de 1, pero no lo veremos sino hasta después de hacer la publicación

![[Pasted image 20240612174244.png]]

La publicación es aceptada con éxito y a la vista no se ve mucha diferencia, ahora si volvemos nuevamente a la caja de comentarios, la alerta nos devuelve el petición que traía el script

![[Pasted image 20240612174311.png]]

![[Pasted image 20240612174336.png]]

# DOM XSS in `document.write` sink using source `location.search`

Esta práctica de laboratorio contiene una vulnerabilidad [de secuencias de comandos entre sitios basada en DOM](https://portswigger.net/web-security/cross-site-scripting/dom-based) en la funcionalidad de seguimiento de consultas de búsqueda. Utiliza la `document.write`función JavaScript, que escribe datos en la página. La `document.write`función se llama con datos de `location.search`, que puede controlar mediante la URL del sitio web.

Para resolver esta práctica de laboratorio, realice un ataque de secuencias de comandos entre sitios que llame a la `alert`función.

Dentro del moto de busqueda hacemos una consulta de algo sin coherencia, vemos el inspeccionar de la pagina y logramos que se ejecuta un script que lleva a la busqueda de lo consultado hacia la ruta /resources/images 

![[Pasted image 20240612182427.png]]

El script nos indicia que sobre la petición que se haga en la caja de búsqueda está será enviada hacia la función trackSearch en donde se diligenciará para conocer si hay algún recurso similar dentro de la ruta que nos muestran 

![[Pasted image 20240612182454.png]]

**`<svg onload=alert(1)>`**:

- `<svg>`: Crea un elemento SVG (Gráficos Vectoriales Escalables). Los elementos SVG son compatibles en la mayoría de los navegadores modernos y permiten el uso de varios atributos y eventos.
- `onload=alert(1)`: Añade un manejador de eventos `onload` que ejecuta JavaScript cuando el elemento SVG se carga. En este caso, el manejador de eventos ejecuta `alert(1)`, que simplemente muestra una alerta con el número `1`

![[Pasted image 20240612183441.png]]

![[Pasted image 20240612183331.png]]


# DOM XSS en el receptor `InnerHTML` usando la ubicación de `location.search`

Esta práctica de laboratorio contiene una vulnerabilidad [de secuencias de comandos entre sitios basada en DOM](https://portswigger.net/web-security/cross-site-scripting/dom-based) en la funcionalidad de búsqueda de blogs. Utiliza una `innerHTML`asignación, que cambia el contenido HTML de un `div`elemento, utilizando datos de `location.search`.

Para resolver esta práctica de laboratorio, realice un ataque de secuencias de comandos entre sitios que llame a la `alert`función.

El comando `<img src=1 onerror=alert(1)>` es un ejemplo clásico de una vulnerabilidad de Cross-Site Scripting (XSS), específicamente de tipo **reflected XSS** o **stored XSS**, dependiendo de cómo se inyecte en la aplicación web. Aquí te explico en detalle qué hace este comando:

### Desglose del Comando

- `<img src=1>`: Este es un elemento HTML de imagen (`img`) con un atributo `src` (source) que apunta a un recurso inexistente (1 no es una URL válida para una imagen).
- `onerror=alert(1)`: Este es un evento de JavaScript que se ejecuta cuando ocurre un error en la carga del `src` de la imagen. Debido a que "1" no es una URL válida, se produce un error, lo que desencadena la ejecución del código JavaScript en el `onerror`.

### Qué Hace el Comando

1. **Creación del Elemento HTML**: Se inserta un elemento de imagen en el DOM (Document Object Model) de la página web.
2. **Intento de Cargar la Imagen**: El navegador intenta cargar el recurso especificado en el atributo `src` ("1" en este caso).
3. **Error de Carga**: Como "1" no es una URL válida, se produce un error de carga.
4. **Ejecución del Código JavaScript**: Debido al error de carga, se activa el evento `onerror`, que ejecuta el código `alert(1)`.
5. **Alerta**: El navegador muestra una alerta con el mensaje "1".

![[Pasted image 20240613081052.png]]
![[Pasted image 20240613081117.png]]

# DOM XSS en el receptor de atributos de anclaje jQuery `href`usando `location.search`la fuente

Esta práctica de laboratorio contiene una vulnerabilidad [de secuencias de comandos entre sitios basada en DOM](https://portswigger.net/web-security/cross-site-scripting/dom-based) en la página de envío de comentarios. Utiliza la `$`función selectora de la biblioteca jQuery para encontrar un elemento ancla y cambia su `href`atributo usando datos de `location.search`.

Para resolver esta práctica de laboratorio, cree la alerta del vínculo "atrás" `document.cookie`.

Este ataque ocurre cuando el script del lado del cliente manipula el DOM de una manera insegura, lo que potencialmente permite a un atacante ejecutar JavaScript arbitrario en el contexto del navegador del usuario. Este tipo de XSS no requiere que una carga maliciosa sea enviada al servidor; en su lugar, se aprovecha de vulnerabilidades en el código del lado del cliente.

- El script lee un parámetro `param` de la cadena de consulta de la URL.
- Luego establece este parámetro como el atributo `href` de un enlace con `id="myLink"`.

Si un atacante controla el valor de `param` en la URL, potencialmente puede inyectar un script malicioso. Por ejemplo, si la URL es:

`http://example.com/?returnPath=javascript:alert(document.cookie)`

`<a id="myLink" href="javascript:alert(document.cookie)">Haz clic aquí</a>`

Cuando el usuario hace clic en el enlace, el código JavaScript `alert(document.cookie)` se ejecuta, demostrando una vulnerabilidad de DOM XSS.

![[Pasted image 20240613090110.png]]
![[Pasted image 20240613091235.png]]
![[Pasted image 20240613091345.png]]

#  DOM XSS en el receptor del selector jQuery usando un evento hashchange

Utiliza `$()`la función selectora de jQuery para desplazarse automáticamente a una publicación determinada, cuyo título se pasa a través de la `location.hash`propiedad.

Para resolver la práctica de laboratorio, entregue un exploit a la víctima que llame a la `print()`función en su navegador.

Para este laboratorio tenemos el siguiente script entregado por la pagina. La función de este script es desplazarnos hacia el post que le indiquemos mediante el `#`, si a la continuación de la almohadilla ponemos algún subtitulo de los post dentro de la pagina este nos lleva a la ubicación, de no ser así pues no tiene ningún efecto

![[Pasted image 20240614095223.png]]

Como ejemplo tenemos la siguiente petición en consola donde buscamos la ubicación del hash y vemos que se guarda en una almohadilla con un subtitulo

![[Pasted image 20240614094830.png]]
![[Pasted image 20240614094917.png]]
![[Pasted image 20240614100013.png]]

1. **Elemento `<iframe>`**:
    
    - Un `iframe` es un elemento HTML que permite incrustar otra página web dentro de la página actual.
    - El `src` especifica la URL de la página que se carga dentro del `iframe`.
2. **`src` Atributo**:
    
    - `src="https://0ab500240497d69e80ac1cab00dd0041.web-security-academy.net/#Festivals"` establece la URL de la página que se cargará dentro del `iframe`.
    - En este caso, la URL contiene un hash `#Festivals`.
    
3. **Evento `onload`**:
    
    - El atributo `onload` especifica un script que se ejecutará cuando el `iframe` haya terminado de cargarse.
    - En este script, `this.src` hace referencia al atributo `src` del `iframe` actual.
    - `this.src += '<img src=x onerror=print()>'` intenta modificar la URL del `src` del `iframe` añadiendo una cadena de texto que incluye un intento de inyección de un script.

El script `this.src += '<img src=x onerror=print()>'` intenta agregar una imagen con un evento `onerror` a la URL del `iframe`. El evento `onerror` está diseñado para ejecutarse cuando hay un error al cargar la imagen, y en este caso, ejecuta la función `print()`.

![[Pasted image 20240614094944.png]]

# XSS reflejado en atributo con corchetes angulares codificados en HTML

Esta práctica de laboratorio contiene una vulnerabilidad [de secuencias de comandos entre sitios reflejada](https://portswigger.net/web-security/cross-site-scripting/reflected) en la funcionalidad de búsqueda del blog donde los corchetes angulares están codificados en HTML. Para resolver esta práctica de laboratorio, realice un ataque de secuencias de comandos entre sitios que inyecte un atributo y llame a la `alert`función.

En esta ocasión tenemos la pagina con nuestro cuadro de búsqueda, indagamos dentro de esta con cualquier petición y que no haya un resultado posible

![[Pasted image 20240614103811.png]]
![[Pasted image 20240614151539.png]]

Dentro del cuadro de búsqueda no podemos hacer lo mismo que el tema de [[XSS reflejado en contexto HTML sin nada codificado]], para si algo similar, dado que el ejemplo anterior no llegue a funcionar hay otra manera que es hacer que una vez el mouse para por la caja de búsqueda se active la alerta, como podemos ver a continuación

![[Pasted image 20240614151858.png]]

# XSS almacenado en un `href`atributo de anclaje con comillas dobles codificadas en HTML

Esta práctica de laboratorio contiene una vulnerabilidad [de secuencias de comandos entre sitios almacenada](https://portswigger.net/web-security/cross-site-scripting/stored) en la funcionalidad de comentarios. Para resolver esta práctica de laboratorio, envíe un comentario que llame a la `alert`función cuando se haga clic en el nombre del autor del comentario.

Para este laboratorio tenemos una vulnerabilidad en la parte de los comentarios dado que en la opcion del nombre y la website se hace una etiqueta de nos redirige a un sitio
![[Pasted image 20240614161953.png]]
![[Pasted image 20240614162220.png]]

Al llenar el formulario lo que hacemos es ingresar una petición en javascript para que nos muestre la alerta, una vez que se publica el comentario al volver a la pagina encontramos nuestro nombre con uno función la cual seria la redirección a lo seria una pagina, pero para este laboratorio nos lanza el cuadro de alerta. 

![[Pasted image 20240614162127.png]]

# XSS reflejado en una cadena de JavaScript con corchetes angulares codificados en HTML

Esta práctica de laboratorio contiene una vulnerabilidad [de secuencias de comandos entre sitios reflejada](https://portswigger.net/web-security/cross-site-scripting/reflected) en la funcionalidad de seguimiento de consultas de búsqueda donde se codifican los corchetes angulares. La reflexión ocurre dentro de una cadena de JavaScript. Para resolver esta práctica de laboratorio, realice un ataque de secuencias de comandos entre sitios que rompa la cadena de JavaScript y llame a la `alert`función.

Este tipo de script es comúnmente utilizado para **rastrear la actividad del usuario** o **recopilar datos** sin necesidad de una interacción visible con el usuario. La imagen en sí es una imagen de un píxel (en este caso, presumiblemente `tracker.gif`), que suele ser invisible o apenas visible.

### Cómo Funciona

- Cuando la página se carga, el script inserta una etiqueta `<img>` en el documento.
- El navegador hace una solicitud HTTP al servidor para obtener la imagen `tracker.gif`, y en la URL incluye los términos de búsqueda como un parámetro de consulta.
- El servidor puede registrar esta solicitud, lo que le permite rastrear qué términos de búsqueda se están utilizando, quién está haciendo la solicitud (basado en la IP, por ejemplo), y otros datos relacionados con la solicitud HTTP

![[Pasted image 20240617101304.png]]
![[Pasted image 20240617101210.png]]

Al enviar la búsqueda `'-alert('hola')-'`, si el valor se codifica correctamente con `encodeURIComponent`, el código no se ejecutará y simplemente se tratará como parte del parámetro de la URL. Sin embargo, si no se codifica, el código podría interpretarse y ejecutarse como JavaScript, lo que podría ser explotado para ataques XSS. Por lo tanto, la codificación y la sanitización de las entradas del usuario son cruciales para prevenir tales vulnerabilidades.

Sin la codificación adecuada, el navegador podría interpretar y ejecutar `'-alert('hola')-'` de la siguiente manera:

`<img src="/resources/images/tracker.gif?searchTerms=' onerror='alert('hola')'>`

![[Pasted image 20240617102009.png]]
![[Pasted image 20240617102031.png]]