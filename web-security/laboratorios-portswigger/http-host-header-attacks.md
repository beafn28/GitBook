# HTTP Host header attacks

## HTTP Host header attacks <a href="#http-host-header-attacks" id="http-host-header-attacks"></a>

### Enunciado

Este laboratorio es vulnerable a _password reset poisoning_. El usuario carlos hará clic sin cuidado en cualquier enlace que reciba por correo electrónico. Inicia sesión en la cuenta de Carlos.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

Cualquier correo enviado a esta cuenta puede leerse a través del cliente de correo en el servidor de exploits.

### Resolución

Pulsamos a olvidar contraseña a través del usuario de prueba y restablecemos la contraseña. Vemos las peticiones.

<figure><img src="../../.gitbook/assets/image (1540).png" alt=""><figcaption></figcaption></figure>

Cambiamos el host con el enlace del exploit server. Tras enviar la petición miramos en los logs y hay una petición GET con su token.

<figure><img src="../../.gitbook/assets/image (1541).png" alt=""><figcaption></figcaption></figure>

Si vamos a peticiones anteriores de restablecer contraseña y cambiamos el token por el anterior podemos enviar perfectamente la petición y cambiar la contraseña de carlos.

<figure><img src="../../.gitbook/assets/image (1542).png" alt=""><figcaption></figcaption></figure>

Nos sale que podemos cambiar la contraseña por lo que la cambiamos e interceptamos para cambiar otra vez el token y le damos a Forward. En definitiva, debemos de interceptar cada petición para cambiar el token.

<figure><img src="../../.gitbook/assets/image (1543).png" alt=""><figcaption></figcaption></figure>

## Lab: Host header authentication bypass

### Enunciado

Este laboratorio hace una suposición sobre el nivel de privilegio del usuario basándose en el encabezado HTTP Host. Accede al panel de administración y elimina al usuario **carlos**.

### Resolución

Mandamos al **Repeater** la petición.

<figure><img src="../../.gitbook/assets/image (1544).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1545).png" alt=""><figcaption></figcaption></figure>

Como dice de usuarios locales puede ser que si las peticiones vienen del mismo servidor no daría problema.

<figure><img src="../../.gitbook/assets/image (1546).png" alt=""><figcaption></figcaption></figure>

Confirmamos la suposición. Entonces interceptando otra vez la petición cambiando eso para eliminar a carlos.

<figure><img src="../../.gitbook/assets/image (1547).png" alt=""><figcaption></figcaption></figure>

