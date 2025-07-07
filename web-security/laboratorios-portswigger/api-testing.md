# API testing

## Lab: Exploiting an API endpoint using documentation

### Enunciado

Para resolver el laboratorio, encuentra la documentación de la API expuesta y elimina al usuario carlos.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos. Normalmente la API está en la ruta **/api**.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Enviamos petición.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Estamos autenticados con el usuario wiener. Sabiendo esto vamos a probar a borrar con el endpoint el usuario carlos (no valida bien los permisos).

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Lo borramos sin problemas.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
