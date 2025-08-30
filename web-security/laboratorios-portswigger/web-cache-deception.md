# Web cache deception

## Lab: Exploiting path mapping for web cache deception

### Enunciado

Para resolver el laboratorio, encuentra la clave API del usuario carlos. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos. Mandamos al **Repeater** la petición y vemos que después de **/my-account** no comprueba nada y si mandamos un fichero JavaScript nos indica cuánto está en caché.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Si mandamos otra vez ahora nos sale **hit** en **X-Cache**. Esto significa que la URL se ha quedado en la caché.

```
<script>
    document.location="https://0ae200710374a63181a8354b001f00b4.web-security-academy.net/my-account/hola.js"
</script>
```

Lo mandamos a la víctima y antes de los 30 segundos de la caché vemos la API de carlos.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
