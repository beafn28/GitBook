# Access control

## Lab: Unprotected admin functionality

### Enunciado

Este laboratorio tiene un panel de administración sin protección. Resuelve el laboratorio eliminando al usuario `carlos`.

### Resolución

Como primer paso vemos el **robots.txt** y nos dice donde se encuentra el panel de administración.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Entramos en el directorio y ya podemos eliminar al usuario.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Unprotected admin functionality with unpredictable URL

### Enunciado

Este laboratorio tiene un panel de administración sin protección. Está ubicado en una ruta impredecible, pero dicha ubicación se revela en algún lugar de la aplicación.

Resuelve el laboratorio accediendo al panel de administración y usándolo para eliminar al usuario `carlos`.

### Resolución

En Target vemos un directorio llamado **/admin-ihvmqe** por lo que accedemos.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: User role controlled by request parameter

### Enunciado

Este laboratorio tiene un panel de administración en la ruta **/admin**, que identifica a los administradores usando una **cookie falsificable**. Resuelve el laboratorio accediendo al panel de administración y usándolo para **eliminar al usuario carlos**. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

### Resolución

Nos logueamos y entramos en el directorio **/admin** y vemos la petición.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Enviamos al **Repeater** y cambiamos el valor de **Admin** a **True** y lo enviamos.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto interceptamos en **Proxy** y le damos a **Forward** cuando cambiemos el valor. Tras esto interceptamos ahora esta petición de a continuación y hacemos lo mismo.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Cambiamos a true y se elimina.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: User role can be modified in user profile

### Enunciado

Este laboratorio tiene un panel de administración en la ruta **/admin**, al que solo pueden acceder los usuarios autenticados con un **`roleid` igual a 2**. Resuelve el laboratorio accediendo al panel de administración y usándolo para **eliminar al usuario carlos**. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

### Resolución

Nos logueamos y cambiamos el correo para fijarnos en su petición.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como vemos en la respuesta tenemos una APIKEY y el rol por lo que voy a probar en la petición enviar el rol añadiéndolo como nos interesa que sea 2 (mandar al **Repeater**).

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Funciona por lo que insertamos la API y el usuario en la petición y lo volvemos e enviarlo.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Entramos **/admin** y ya podemos eliminarlo.

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: User ID controlled by request parameter

### Enunciado

Este laboratorio tiene una vulnerabilidad de **escalada horizontal de privilegios** en la página de cuenta de usuario. **Obtener la clave API del usuario `carlos`** y enviarla como solución. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

### Resolución

Nos logueamos con esas credenciales y vemos la petición que se identifica el usuario con el parámetro `?id=wiener`. Lo envío al Repeater y lo igualo a Carlos y lo envio.

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Subimos la solución.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: User ID controlled by request parameter, with unpredictable user IDs

### Enunciado

Este laboratorio tiene una vulnerabilidad de **escalada horizontal de privilegios** en la página de cuenta de usuario, pero identifica a los usuarios mediante **GUIDs**. **Encontrar el GUID de `carlos`** y luego **enviar su clave API como solución**. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

### Resolución

Nos logueamos y revisamos el blog al parecer el usuario carlos escribió algún post por lo que miramos la petición.

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Por lo que cogemos el ID de **carlos** y en la petición de **wiener** mandamos  a **Repeater** y claro cambiamos por el de **carlos** y lo enviamos.

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: User ID controlled by request parameter with data leakage in redirect

### Enunciado

Este laboratorio contiene una **vulnerabilidad de control de acceso**, donde se filtra información sensible en el **cuerpo de una respuesta de redirección**. **Obtener la clave API del usuario `carlos`** y enviarla como solución. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

### Resolución

Nos logueamos y miramos la petición para cambiar el id de wiener a carlos y enviar la petición (se hace en **Repeater**).

<figure><img src="../../.gitbook/assets/image (18) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: User ID controlled by request parameter with password disclosure

### Enunciado

Este laboratorio tiene una página de cuenta de usuario que muestra la **contraseña actual del usuario prellenada en un campo enmascarado**. **Recuperar la contraseña del administrador** y usarla para **eliminar al usuario `carlos`**. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

### Resolución

Nos logueamos y vemos la petición. Cambiamos de nuevo el ID de wiener a administrator.

<figure><img src="../../.gitbook/assets/image (21) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como nos dice el enunciado la contraseña está enmascarada por lo que vamos a ver el código.

<figure><img src="../../.gitbook/assets/image (22) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos volvemos a loguear con las credenciales y ya podemos acceder al panel y borrar el usuario.

<figure><img src="../../.gitbook/assets/image (23) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Insecure direct object references

### Enunciado

Este laboratorio **almacena los registros de chat de los usuarios directamente en el sistema de archivos del servidor** y los recupera mediante **URLs estáticas**. **Encontrar la contraseña del usuario `carlos`** y **acceder a su cuenta**.

### Resolución

Enviamos un mensaje y vemos la petición ya que se puede descargar el archivo. Cambiamos a **1.tx**t y enviamos la petición.

<figure><img src="../../.gitbook/assets/image (24) (1) (1).png" alt=""><figcaption></figcaption></figure>

Copiamos la contraseña y entramos con esas credenciales.

<figure><img src="../../.gitbook/assets/image (25) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: URL-based access control can be circumvented

### Enunciado

Este sitio web tiene un panel de administración no autenticado en /admin, pero se ha configurado un sistema de front-end para bloquear el acceso externo a esa ruta. Sin embargo, la aplicación en el back-end está construida sobre un framework que admite la cabecera X-Original-URL.

Para resolver el laboratorio, accede al panel de administración y elimina al usuario carlos.

### Resolución

Para explotar esta vulnerabilidad de control de acceso basado en URL, identificamos que el servidor back-end respeta la cabecera `X-Original-URL`, incluso cuando el front-end bloquea el acceso directo a `/admin`. En el ataque, se envió una solicitud con la cabecera `X-Original-URL: /admin` para acceder al panel de administración que normalmente estaba bloqueado externamente. Luego, para eliminar al usuario `carlos`, se usó `X-Original-URL: /admin/delete?username=carlos`, logrando así ejecutar la acción privilegiada burlando la validación del front-end.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Method-based access control can be circumvented

### Enunciado

Este laboratorio implementa controles de acceso basados en parte en el método HTTP de las solicitudes. Puedes familiarizarte con el panel de administración iniciando sesión con las credenciales administrator:admin.

Para resolver el laboratorio, inicia sesión usando las credenciales wiener:peter y explota los controles de acceso defectuosos para ascenderte a administrador.

### Resolución

Como administrador puedes cambiar el rol de un usuario enviando un **POST** a **/admin-roles** con el usuario y la acción. Si cerramos sesión e iniciamos como **wiener**, podemos intentar escalar privilegios. Notamos que **/admin-roles** acepta **GET** sin parámetros. Si probamos **POST** como wiener, nos da **Unauthorized**. Pero el **GET** funciona. Entonces enviamos un **GET** a **/admin-roles?username=wiener\&action=upgrade** y logramos la escalada.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Multi-step process with no access control on one step

### Enunciado

Este laboratorio tiene un panel de administración con un proceso en varios pasos para cambiar el rol de usuario, pero está mal implementado. Puedes explorar el panel iniciando sesión como **administrator:admin**.

Para resolver el lab, inicia sesión como **wiener:peter** y aprovecha el fallo en los controles de acceso para ascender tu cuenta al rol de administrador.

### Resolución

Primero, iniciamos sesión como **administrator** para ver cómo funciona el panel. El administrador puede **ascender o degradar** usuarios enviando un **POST a /admin-roles** con `username` y `action`.

En el flujo normal, tras el primer POST se muestra una página de confirmación, y al confirmar se envía otro **POST con `confirmed=true`**.

Descubrimos esto interceptando las peticiones.

Luego, iniciamos sesión como **wiener** e intentamos escalar privilegios:

* Los **GET y POST** simples a **/admin-roles** devolvían **Unauthorized**.
* Pero al enviar un **POST con `username`, `action=upgrade` y `confirmed=true`**, el servidor aceptó la petición sin verificar la confirmación.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Referer-based access control

### Enunciado

Este laboratorio controla el acceso a ciertas funciones de administrador basándose en el encabezado Referer. Puedes familiarizarte con el panel de administración iniciando sesión con las credenciales **administrator:admin**.

Para resolver el laboratorio, inicia sesión con las credenciales **wiener:peter** y explota los controles de acceso defectuosos para ascenderte a administrador.

### Resolución

Para resolver este lab primero entramos como **administrator:admin** y vamos al panel de admin para ver cómo se hace un upgrade de usuario observamos que la petición al endpoint `/admin-roles` lleva un **Referer** que indica que viene de `/admin` luego cerramos sesión iniciamos como **wiener:peter** y repetimos la misma petición pero con la cookie de wiener y el parámetro **username=wiener** manteniendo el mismo **Referer** así el servidor cree que la petición viene del panel admin y nos convierte en administrador.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
