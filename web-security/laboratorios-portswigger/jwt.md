# JWT

## Lab: JWT authentication bypass via unverified signature

### Enunciado

Este laboratorio utiliza un mecanismo basado en JWT para gestionar las sesiones. Debido a fallos en la implementación, el servidor no verifica la firma de los JWT que recibe. Modifica tu token de sesión para obtener acceso al panel de administración en `/admin`, y luego elimina al usuario carlos.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Vemos la petición y las partes del JWT.

<figure><img src="../../.gitbook/assets/image (1551).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1552).png" alt=""><figcaption></figcaption></figure>

Vamos a inteptar el tráfico y cambiamos al usuario administrator.

<figure><img src="../../.gitbook/assets/image (1553).png" alt=""><figcaption></figcaption></figure>

Lo mismo hacemos con ese cambio para eliminarlo.

<figure><img src="../../.gitbook/assets/image (1554).png" alt=""><figcaption></figcaption></figure>

## Lab: JWT authentication bypass via flawed signature verification

### Enunciado

Este laboratorio utiliza un mecanismo basado en JWT para gestionar las sesiones. El servidor está configurado de forma insegura para aceptar JWT sin firma. Modifica tu token de sesión para obtener acceso al panel de administración en `/admin` y luego elimina al usuario carlos.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos e interceptamos en **/admin**.

<figure><img src="../../.gitbook/assets/image (1555).png" alt=""><figcaption></figcaption></figure>

No nos deja hacer lo del anterior laboratorio. Volvemos a autenticarnos. Hay que cambiar tanto el usuario y eliminar la parte de firma y cambiar que no hay algoritmo de firma.

<figure><img src="../../.gitbook/assets/image (1556).png" alt=""><figcaption></figcaption></figure>

Hacemos los mismos cambios en la petición de eliminar a carlos.

<figure><img src="../../.gitbook/assets/image (1557).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1558).png" alt=""><figcaption></figcaption></figure>

