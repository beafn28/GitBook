# Nivel 0-5

### NIVEL 0

Ingresamos las credenciales para el nivel 0.

* **Usuario:** `natas0`
* **Contraseña:** `natas0`

<figure><img src="../../../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 0**. El mensaje indica que la contraseña para el siguiente nivel está en la página, así que revisamos el código fuente. Ahí, comentada, se encuentra la contraseña para avanzar al siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (336).png" alt=""><figcaption></figcaption></figure>

### NIVEL 1

Ingresamos las credenciales para el nivel 1.

* **Usuario:** `natas1`
* **Contraseña:** `0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq`

Tras iniciar sesión, verás la página de inicio del **Nivel 1** con un mensaje que indica que el clic derecho está bloqueado. Sin embargo, menciona que la contraseña está en la página web. Para encontrarla, utiliza `Ctrl+Shift+I` para abrir las herramientas de desarrollo y examinar el código fuente en busca de la contraseña del próximo nivel.

<figure><img src="../../../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

### NIVEL 2

Ingresamos las credenciales para el nivel 2.

* **Usuario:** `natas2`
* **Contraseña:** `TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI`

Tras iniciar sesión, verás la página de inicio del **Nivel 2** con un mensaje que dice que no hay nada en la página web. Al inspeccionar el código fuente, notarás un directorio que parece interesante. Vamos a investigarlo más a fondo.

<figure><img src="../../../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (341).png" alt=""><figcaption></figcaption></figure>

Al explorar el directorio, encontramos dos archivos. Uno de ellos es **users.txt**, que nos llama la atención. Decidimos abrirlo para ver si contiene la contraseña para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (342).png" alt=""><figcaption></figcaption></figure>

### NIVEL 3

Ingresamos las credenciales para el nivel 3.

* **Usuario:** `natas3`
* **Contraseña:** `3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH`

Tras iniciar sesión, verás la página de inicio del **Nivel 3** con un mensaje que dice que no hay nada en esta página. Sin embargo, al revisar el código fuente, encontramos un mensaje que llama nuestra atención y podría contener una pista importante.

<figure><img src="../../../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>

La pista **"Not even Google will find it this time…"** sugiere que la información relevante puede estar en el archivo `robots.txt`. Este archivo es utilizado por los servidores web para dar instrucciones a los motores de búsqueda sobre qué páginas o archivos no deben ser indexados.

<figure><img src="../../../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>

Por lo que entramos en ese directorio para indagar si hay algún archivo interesante que tenga la contraseña.

<figure><img src="../../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

Al ser **users.txt** el único archivo entramos en él donde definitivamente se encuentra la contraseña para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>

### NIVEL 4

Ingresamos las credenciales para el nivel 4.

* **Usuario:** `natas4`
* **Contraseña:** `QryZXc2e0zahULdHrtHxzyYkj59kUxLQ`

Tras iniciar sesión, verás la página de inicio del **Nivel 4** con un mensaje que indica que debes acceder desde **http://natas5.natas.labs.overthewire.org/**. Para superar este nivel, necesitas manipular el encabezado **Referer** de tus solicitudes. Aquí te explico cómo hacerlo utilizando **Burp Suite**, una herramienta de proxy popular para análisis de seguridad web.

1. **Inicia Burp Suite**: Asegúrate de que Burp esté en funcionamiento y configurado para interceptar tus solicitudes.
2. **Recarga el sitio web**: Visita la página del nivel 4 en tu navegador.
3. **Verifica el paquete en Burp**: Ve a la pestaña **Proxy > Intercept** en Burp Suite. Deberías ver la solicitud interceptada con la siguiente información del encabezado del paquete.
4. **Modifica el encabezado Referer**: Cambia el valor del encabezado **Referer** a `http://natas5.natas.labs.overthewire.org/`.
5. **Envía la solicitud**: Una vez modificado el encabezado, haz clic en **Send** para enviar la solicitud modificada.

Siguiendo estos pasos, deberías poder acceder al contenido restringido y obtener la contraseña para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-18 163839.png" alt=""><figcaption></figcaption></figure>

Tras esto miramos **HTTP history** y mandamos una petición al **Repeater**.

<figure><img src="../../../.gitbook/assets/image (328).png" alt=""><figcaption></figcaption></figure>

Tras mandarlo al **Repeater**, cambiamos el encabezado **Referer**. Como puedes ver, el campo **Referer** del paquete está configurado en: `http://natas4.natas.labs.overthewire.org/`. Vamos a cambiarlo a `http://natas5.natas.labs.overthewire.org/`.

Una vez hecho esto, haz clic en **Send**, y deberíamos obtener la contraseña.

<figure><img src="../../../.gitbook/assets/image (329).png" alt=""><figcaption></figcaption></figure>

### NIVEL 5

Ingresamos las credenciales para el nivel 5.

* **Usuario:** `natas5`
* **Contraseña:** `0n35PkggAPm2zbEpOU802c0x0Msn1ToK`

Tras iniciar sesión, verás la página de inicio del **Nivel 5** con un mensaje que indica que el acceso ha sido denegado.

<figure><img src="../../../.gitbook/assets/image (330).png" alt=""><figcaption></figcaption></figure>

Nuevamente, podemos ver que no tenemos acceso a la página web porque no hemos iniciado sesión. Vamos a abrir **Burp Suite** y ver si podemos interceptar el paquete para **natas5**.

<figure><img src="../../../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>

Como podemos ver, el encabezado del paquete almacena información de la cookie. Cambiemos `loggedin=0` a `loggedin=1`, y reenviemos ese paquete en el **Repeater** para obtener la contraseña del nuevo nivel.<br>

<figure><img src="../../../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>

