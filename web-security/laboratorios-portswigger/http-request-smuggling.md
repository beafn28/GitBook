# HTTP request smuggling

## Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses

### Enunciado

Este laboratorio tiene un escenario con un servidor front-end y uno back-end. El servidor front-end **no admite codificación chunked**.

Para resolver el laboratorio, debes **realizar un ataque de request smuggling** (inyección de petición encubierta) al servidor back-end, de forma que **la siguiente petición para la raíz (/) genere una respuesta 404 Not Found**.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas **solo posibles en HTTP/1**. Puedes cambiar manualmente de protocolo en **Burp Repeater**, en la sección _Request attributes_ del panel _Inspector_.

**Sugerencia**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede resultar complicado. Para facilitar este trabajo, Burp ofrece la extensión **HTTP Request Smuggler**, disponible en el **BApp Store**.

### Resolución

El frontend no admite Transfer-Encoding: chunked en métodos válidos como POST. Sin embargo, no valida correctamente métodos no estándar. Al usar GPOST se evita su validación de Transfer-Encoding. El frontend ve Content-Length y delimita la petición basándose en ese valor, ignorando Transfer-Encoding por no reconocer el método.

El backend, en cambio, procesa Transfer-Encoding: chunked. Al recibir la cabecera Transfer-Encoding: chunked, ignora Content-Length y parsea el cuerpo como chunks.

La parte inicial hasta el 0 se interpreta como un cuerpo chunked vacío. El backend ve 0 y cierra el cuerpo de la primera petición. Lo que sigue (GET /404 HTTP/1.1) es tratado como una petición nueva independiente.

Así se inyecta la segunda petición. El backend la procesa, devolviendo la respuesta 404 solicitada en el enunciado.

El frontend no detecta ni bloquea la inyección porque delimita el cuerpo con Content-Length sin interpretar Transfer-Encoding. El backend interpreta el cuerpo con Transfer-Encoding y divide las solicitudes, revelando la vulnerabilidad.

El resultado es que la siguiente petición al backend se contamina con la inyección enviada, generando la respuesta 404 y confirmando el ataque de request smuggling.

<figure><img src="../../.gitbook/assets/image (1580).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1581).png" alt=""><figcaption></figcaption></figure>

## Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses

### Enunciado

Este laboratorio involucra un servidor _front-end_ y uno _back-end_. En este caso, el **servidor back-end no admite codificación chunked**.

Para resolver el laboratorio, debes **realizar un ataque de request smuggling** (inyección de petición encubierta) para que **la siguiente solicitud a la raíz (/) devuelva un error 404 Not Found**.

> Aunque el laboratorio soporta HTTP/2, la solución prevista **solo es posible con técnicas de HTTP/1**. Puedes cambiar manualmente el protocolo en **Burp Repeater**, en la sección _Request attributes_ del panel _Inspector_.

**Sugerencia**\
Ajustar manualmente los campos de longitud en ataques de _request smuggling_ puede ser complicado. Para ello, puedes usar la extensión de Burp llamada **HTTP Request Smuggler**, disponible en el **BApp Store**.

### Resolución

Primero, probaremos el ataque **TE.CL** (Transfer-Encoding/Content-Length), donde el _front-end_ usa **Transfer-Encoding** y el _back-end_ usa **Content-Length**.

Pero antes de eso, asegúrate de **desactivar la opción "Update Content-Length"** y de **mostrar los caracteres no imprimibles**.

El servidor frontend respetaba el encabezado Transfer-Encoding y procesaba el cuerpo como chunked mientras que el backend solo miraba Content-Length y esperaba un cuerpo corto de cuatro bytes así conseguimos que el frontend terminara su lectura dejando el request smuggled preparado para el backend que lo trató como una nueva petición separada rompiendo la secuencia y provocando que la siguiente solicitud legítima a la raíz devolviera un 404 Not Found demostrando que pudimos inyectar un request falso entre ambos servidores y confirmar la vulnerabilidad

<figure><img src="../../.gitbook/assets/image (1582).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1583).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability

### Enunciado

Este laboratorio tiene un servidor front-end y uno back-end. El front-end no admite codificación chunked. Existe un panel de administración en **/admin**, pero el front-end bloquea el acceso a esa ruta.

Para resolver el lab, debemos **inyectar (smuggle) una petición** al servidor back-end que acceda al panel de administración y elimine al usuario **carlos**.

> Aunque el lab soporta HTTP/2, la solución prevista usa técnicas que solo son posibles en **HTTP/1**. Puedes cambiar de protocolo en Burp Repeater, en la sección **Request attributes** del panel **Inspector**.

**Sugerencia:**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede ser complicado. El complemento **HTTP Request Smuggler** de Burp está diseñado para ayudar. Puedes instalarlo desde el **BApp Store**.

### Resolución

<figure><img src="../../.gitbook/assets/image (1584).png" alt=""><figcaption></figcaption></figure>

Nos bloquea el acceso por lo que mandamos la petición al Repeater y testeamos HTTP smuggling. El front-end ve **Content-Length** y lee solo la parte inicial (ignorando la parte smuggled).\
El back-end, que respeta **Transfer-Encoding**, lee el cuerpo en chunked, interpretando los datos inyectados como una segunda petición.

<figure><img src="../../.gitbook/assets/image (1585).png" alt=""><figcaption></figcaption></figure>

Al enviarlo, el front-end solo procesa la primera parte, pero el back-end recibe la petición GET /admin y devuelve el contenido del panel de admin. En la respuesta vemos la lista de usuarios (wiener y carlos) y la opción de borrar.

<figure><img src="../../.gitbook/assets/image (1586).png" alt=""><figcaption></figcaption></figure>

Empaquetamos esa línea como el **chunk smuggled**, manteniendo bien formateadas las cabeceras y longitudes. El front-end ignora este contenido extra. El back-end, sin el filtro del front-end, procesa la petición de borrado y elimina al usuario **carlos**.

<figure><img src="../../.gitbook/assets/image (1587).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1588).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability

### Enunciado

Este laboratorio tiene un servidor **front-end** y uno **back-end**. El **back-end** **no soporta** codificación chunked. Hay un panel de administración en **/admin**, pero el **front-end** **bloquea el acceso directo** a esa ruta. Para resolver el lab, necesitamos **infiltrar (smuggle) una petición** hacia el back-end que acceda a **/admin** y elimine al usuario **carlos**.

> Aunque el laboratorio admite HTTP/2, la solución correcta **requiere técnicas solo posibles en HTTP/1**.\
> Puedes cambiar el protocolo manualmente en **Burp Repeater**, en la sección **Request attributes** del **Inspector**.

**Sugerencia**\
Ajustar manualmente los valores de longitud en los ataques de request smuggling puede ser complicado.\
La extensión **HTTP Request Smuggler** para Burp Suite está diseñada para facilitar este proceso.\
Puedes instalarla desde el **BApp Store**.

### Resolución

El filtro en el front-end impide el acceso directo.

<figure><img src="../../.gitbook/assets/image (1589).png" alt=""><figcaption></figcaption></figure>

Primero identificamos que el front-end del sitio bloquea directamente cualquier intento de acceder al path /admin devolviendo el mensaje de error "Path /admin is blocked". Sabemos por la descripción del laboratorio que existe un front-end y un back-end y que el back-end no aplica ese filtro pero además no soporta chunked encoding mientras que el front-end sí. Esa diferencia la podemos aprovechar con un ataque de HTTP Request Smuggling usando la técnica TE.CL para inyectar una petición prohibida que solo el back-end procesará.

<figure><img src="../../.gitbook/assets/image (1590).png" alt=""><figcaption></figcaption></figure>

Para preparar el ataque construimos una petición POST con Transfer-Encoding: chunked y un Content-Length pequeño que engaña al front-end. Escribimos el body de forma que declare un chunk de tamaño suficiente para incluir nuestra petición smuggled completa. El front-end procesa el body usando los chunks, separa la primera petición y la reenvía al back-end. El back-end, al no entender chunked, se guía por el Content-Length y lee menos datos de los que realmente enviamos, dejando en cola la segunda petición que en realidad es el GET /admin. Así logramos que el back-end ejecute la petición prohibida sin que el front-end la bloquee.

<figure><img src="../../.gitbook/assets/image (1591).png" alt=""><figcaption></figcaption></figure>

Al conseguir que el back-end responda al GET /admin pudimos ver la lista de usuarios, confirmando la presencia de carlos. En ese punto repetimos el mismo proceso pero esta vez modificando el contenido smuggled para que la segunda petición sea un GET /admin/delete?username=carlos. El cuerpo chunked incluía esa petición con todos los headers necesarios, engañando de nuevo al front-end que cortaba antes por Content-Length y dejando la petición de borrado lista para el back-end. El back-end la procesaba sin filtrar el path y eliminaba al usuario carlos.

<figure><img src="../../.gitbook/assets/image (1592).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting HTTP request smuggling to reveal front-end request rewriting

### Enunciado

Este laboratorio involucra un servidor front-end y un servidor back-end, y el servidor front-end no admite codificación chunked.

Hay un panel de administración en /admin, pero solo es accesible para personas con la dirección IP 127.0.0.1. El servidor front-end agrega un encabezado HTTP a las solicitudes entrantes que contiene su dirección IP. Es similar al encabezado X-Forwarded-For pero tiene un nombre diferente.

Para resolver el laboratorio, haz un ataque de request smuggling hacia el servidor back-end que revele el nombre del encabezado que agrega el front-end. Luego, haz otro ataque de request smuggling que incluya ese encabezado, acceda al panel de administración y elimine al usuario carlos.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas que solo son posibles en HTTP/1. Puedes cambiar manualmente de protocolo en Burp Repeater desde la sección de atributos de la solicitud en el panel Inspector.

**Sugerencia**\
Ajustar manualmente los campos de longitud en los ataques de request smuggling puede ser complicado. Nuestra extensión HTTP Request Smuggler para Burp fue diseñada para ayudar con esto. Puedes instalarla desde la BApp Store.

### Resolución

<figure><img src="../../.gitbook/assets/image (1593).png" alt=""><figcaption></figcaption></figure>

Identificamos la cabecera que el front-end añade con la IP del cliente. Para lograrlo, enviamos un ataque de HTTP request smuggling usando TE.CL, donde la parte smuggled es un POST a la raíz (/) con un cuerpo controlado que incluía `search=test`. Al forzar al back-end a interpretar esta sección como una petición separada, conseguimos que la respuesta nos devolviera un buscador con el contenido del request parseado. Ahí vimos reflejada la cabecera `oabCwn-Ip` con nuestro valor de IP pública. Gracias a este comportamiento, pudimos deducir el nombre exacto del header que el front-end reescribe y usa para pasar la IP al back-end.

<figure><img src="../../.gitbook/assets/image (1594).png" alt=""><figcaption></figcaption></figure>

Modificamos el ataque smuggling para que la petición smuggled fuera un GET a `/admin`, e incluimos manualmente la cabecera `oabCwn-Ip: 127.0.0.1`. Esto engañó al back-end haciéndole creer que la solicitud provenía de localhost. Como resultado, en la respuesta vimos que se cargaba correctamente el panel de administración. De esta forma comprobamos que habíamos burlado el control de acceso basado en la IP del cliente.

<figure><img src="../../.gitbook/assets/image (1595).png" alt=""><figcaption></figcaption></figure>

Aquí enviamos otro ataque smuggling pero esta vez la petición smuggled fue un GET a `/admin/delete?username=carlos`, manteniendo la cabecera `oabCwn-Ip: 127.0.0.1`. Gracias a esto, el back-end procesó la eliminación del usuario Carlos como si la petición proviniera de localhost. En la respuesta obtuvimos un redireccionamiento 302 a `/admin`, confirmando que la eliminación se había realizado y que habíamos completado con éxito el objetivo del lab.

<figure><img src="../../.gitbook/assets/image (1596).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1597).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting HTTP request smuggling to capture other users' requests

### Enunciado

Este laboratorio involucra un servidor front-end y un servidor back-end, y el servidor front-end **no admite codificación chunked**.

Para resolver el laboratorio, debes hacer un ataque de request smuggling al servidor back-end que provoque que la siguiente petición del usuario víctima se almacene en la aplicación. Luego, recupera la petición del usuario víctima y usa sus cookies para acceder a su cuenta.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas que solo son posibles en HTTP/1. Puedes cambiar manualmente de protocolo en Burp Repeater desde la sección de atributos de la solicitud en el panel Inspector.\
> El laboratorio simula la actividad de un usuario víctima. Cada cierto número de solicitudes POST que hagas al laboratorio, el usuario víctima realizará su propia solicitud. Es posible que necesites repetir tu ataque varias veces para asegurarte de que la solicitud del usuario víctima ocurra como se requiere.

**Sugerencia**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede ser complicado. Nuestra extensión **HTTP Request Smuggler** para Burp fue diseñada para ayudar con esto. Puedes instalarla desde la BApp Store.

### Resolución

Enviamos un POST normal a `/post/comment` para simular un comentario legítimo. El servidor nos devuelve un **302 Found** con la redirección a `/post/comment/confirmation`, confirmando que el comentario se ha procesado bien. Esto nos sirve para ver la estructura del request que luego vamos a querer capturar.

<figure><img src="../../.gitbook/assets/image (1598).png" alt=""><figcaption></figcaption></figure>

Construimos la petición smuggling en HTTP/1.1 usando Transfer-Encoding: chunked. La idea es inyectar dentro del cuerpo del request otro POST hacia `/post/comment`, para que se quede almacenado en el socket del backend y sea procesado cuando llegue la siguiente petición (del usuario víctima). Observamos el Content-Length elevado y el body con el POST incrustado. En la respuesta, vemos otro **302 Found** que nos confirma que la inyección llegó hasta el backend.

<figure><img src="../../.gitbook/assets/image (1600).png" alt=""><figcaption></figcaption></figure>

Volvemos a hacer el smuggling, pero ahora con otro comentario en el cuerpo inyectado. El servidor sigue aceptándolo. En estas repeticiones, ajustamos el contenido inyectado para perfeccionar la captura de la sesión del siguiente usuario que interactúe con la aplicación.

<figure><img src="../../.gitbook/assets/image (1601).png" alt=""><figcaption></figcaption></figure>

Vemos que finalmente logramos capturar la **request completa del usuario víctima**. El servidor nos responde **200 OK** y en el body podemos ver el request del otro usuario, incluyendo su cookie de sesión y otros headers. Este paso confirma que el ataque de request smuggling ha funcionado: hemos interceptado la petición del usuario víctima.

<figure><img src="../../.gitbook/assets/image (1602).png" alt=""><figcaption></figcaption></figure>

Cuando conseguimos capturar la cookie de sesión del usuario víctima a través del ataque de request smuggling, copiamos su valor y lo reemplazamos en nuestro navegador usando las herramientas de desarrollador (inspector). De esta forma, al actualizar la página, el servidor nos reconoce como el usuario víctima y logramos acceder a su cuenta.

<figure><img src="../../.gitbook/assets/image (1603).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting HTTP request smuggling to deliver reflected XSS

### Enunciado

Este laboratorio tiene un servidor frontal (front-end) y un servidor trasero (back-end), y el servidor frontal **no admite codificación chunked**.

La aplicación también es vulnerable a **XSS reflejado** a través del **header User-Agent**.

Para resolver el laboratorio, debes **inyectar (smuggle) una petición** al servidor back-end que provoque que la siguiente petición de un usuario reciba una respuesta que contenga un exploit XSS que ejecute `alert(1)`.

> Aunque el laboratorio soporta HTTP/2, la solución prevista requiere técnicas que **solo son posibles en HTTP/1**. Puedes cambiar manualmente el protocolo en Burp Repeater en la sección **Request attributes** del panel **Inspector**.

El laboratorio simula la actividad de un usuario víctima. Cada ciertas peticiones POST que hagas al laboratorio, el usuario víctima hará su propia petición. Puede que tengas que repetir el ataque varias veces hasta que ocurra la petición de la víctima en el momento adecuado.

**Sugerencia:**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede ser complicado. Nuestra extensión **HTTP Request Smuggler** para Burp está diseñada para ayudar en esto. Puedes instalarla desde el **BApp Store**.

### Resolución

Miramos código fuente.

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Una petición _smuggled_ en formato **TE.CL**, declarando `Transfer-Encoding: chunked` para que el front-end lo interpretara como chunked mientras que el back-end usaba `Content-Length` para separar el cuerpo real. En el cuerpo inyectamos la siguiente petición secundaria:

```
GET /post?postId=10 HTTP/1.1
User-Agent: "><script>alert(1)</script>
```

Esta petición secuestrada queda encolada en el servidor, de forma que cuando la siguiente víctima legítima solicita el post con `postId=10`, el servidor le sirve la respuesta que contiene el `User-Agent` inyectado.

La respuesta del servidor muestra un **200 OK** con contenido HTML que incluye nuestro payload de XSS, confirmando que la inyección fue exitosa. En este escenario el atacante logra ejecutar `alert(1)` en el navegador de la víctima al aprovechar la confianza del back-end en la cabecera `User-Agent` y el mal manejo de la separación de peticiones entre el front-end y el back-end.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Response queue poisoning via H2.TE request smuggling

### Enunciado

Este laboratorio es vulnerable a _request smuggling_ porque el servidor front-end convierte solicitudes HTTP/2 en HTTP/1 incluso si tienen una longitud ambigua.

Para resolver el laboratorio, elimina al usuario carlos usando _response queue poisoning_ para acceder al panel de administración en /admin. Un usuario administrador iniciará sesión aproximadamente cada 15 segundos.

La conexión con el servidor back-end se reinicia cada 10 solicitudes, así que no te preocupes si queda en un estado incorrecto: solo envía algunas solicitudes normales para obtener una conexión nueva.

### Resolución

Empezamos construyendo un ataque de _request smuggling_ usando H2.TE. En la primera petición de la captura se ve que enviamos un `POST` en HTTP/2 con los encabezados `Transfer-Encoding: chunked` y `Content-Length: 84`. El objetivo aquí es crear una ambigüedad para que el front-end (que acepta HTTP/2) degrade la petición al back-end en HTTP/1.1 pero trate el cuerpo como chunked, mientras que el back-end usa el `Content-Length`. Con esto logramos desincronizar la interpretación de los límites del cuerpo entre los servidores. El resultado fue exitoso: en la primera respuesta se muestra un `200 OK` que incluye en los headers una cookie de sesión de un usuario administrador. Esa cookie aparece en la cabecera `Set-Cookie`, y la capturamos para usarla más adelante.&#x20;

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Repetimos el mismo ataque de smuggling para aprovechar la sesión del administrador. Esta vez, tras enviar la petición manipulada, recibimos un `302 Found` que nos redirige a `/my-account?id=administrator`, confirmando que la sesión interceptada es válida y pertenece al admin.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Copiamos la cookie de administrador y la pegamos en nuestra sesión del navegador o en el cliente HTTP. Así, al acceder al panel de administración, eliminamos al usuario carlos con éxito.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: H2.CL request smuggling

### Enunciado

Este laboratorio es vulnerable a _request smuggling_ porque el servidor front-end degrada las solicitudes HTTP/2 incluso si tienen una longitud ambigua.

Para resolver el laboratorio, realiza un ataque de _request smuggling_ que haga que el navegador de la víctima cargue y ejecute un archivo JavaScript malicioso desde el servidor de explotación, llamando a `alert(document.cookie)`. El usuario víctima accede a la página de inicio cada 10 segundos.

### Resolución

Para resolver el lab, realizamos un ataque de request smuggling usando HTTP/2 con Content-Length ambiguo para inyectar una segunda petición hacia el backend. En la primera captura probamos con un GET sencillo para ajustar el contenido smuggled y verificamos que llegaba al backend (aunque devolvía 404). En la segunda captura construimos la petición final: tras el POST/HTTP/2 con chunked encoding, inyectamos un GET a /resources/js en nuestro exploit server. Allí servimos el payload JavaScript con `alert(document.cookie)`. Cuando la víctima visitó la página, el backend entregó nuestro archivo malicioso y se ejecutó en su navegador, resolviendo el lab.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: HTTP/2 request smuggling via CRLF injection

### Enunciado

Este laboratorio es vulnerable a request smuggling porque el servidor front-end convierte las solicitudes HTTP/2 en HTTP/1 y no sanea adecuadamente las cabeceras entrantes.

Para resolver el laboratorio, usa un vector de request smuggling exclusivo de HTTP/2 para obtener acceso a la cuenta de otro usuario. La víctima accede a la página de inicio cada 15 segundos.

Si no estás familiarizado con las funciones exclusivas de Burp para pruebas con HTTP/2, consulta la documentación para ver los detalles sobre cómo usarlas.

### Resolución

Vemos un intento fallido donde enviamos en la petición una cabecera `Test`, pero el servidor respondió con error 404.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Aprovechamos el _kettling_ para introducir un salto de línea literal (`\n`) dentro del valor de la cabecera, convirtiéndolo al ser downgraded a HTTP/1 en un separador válido de cabeceras. Así logramos inyectar `Transfer-Encoding: chunked` en la parte de las cabeceras del back-end, activando un _TE.CL desync_. En esta petición el servidor aceptó la carga y devolvió código 200 OK junto con el contenido HTML del perfil de la víctima, confirmando que la sesión del usuario Carlos había sido secuestrada.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Logramos acceder como el usuario usando su cookie.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: HTTP/2 request splitting via CRLF injection

### Enunciado

Este laboratorio es vulnerable a **request smuggling** porque el servidor front-end convierte las solicitudes HTTP/2 en HTTP/1.1 y no sanea adecuadamente las cabeceras entrantes.

Para resolver el laboratorio, elimina al usuario **carlos** utilizando **response queue poisoning** para acceder al panel de administración en **/admin**. Un usuario administrador iniciará sesión aproximadamente cada 10 segundos.

La conexión con el servidor back-end se reinicia cada 10 solicitudes, así que no te preocupes si queda en un estado inconsistente: solo envía algunas peticiones normales para conseguir una conexión limpia.

### Resolución

Enviamos un encabezado personalizado llamado `Test`El servidor devuelve un error 404 "Not Found", lo que confirma que el backend interpreta nuestra inyección como una petición independiente.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ajustamos las cabeceras. El servidor devuelve un 302 de redirección, lo que indica que se procesó correctamente.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Con la cookie logramos acceder al panel de admin.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: CL.0 request smuggling

### Enunciado

Este laboratorio es vulnerable a ataques de _request smuggling_ del tipo CL.0. El servidor back-end ignora el encabezado Content-Length en solicitudes hacia ciertos endpoints.

Para resolver el laboratorio, identifica un endpoint vulnerable, _smugglea_ (inyecta) una solicitud hacia el back-end para acceder al panel de administración en /admin y luego elimina al usuario carlos.

Este laboratorio se basa en vulnerabilidades reales descubiertas por PortSwigger Research. Para más detalles, consulta **Browser-Powered Desync Attacks: A New Frontier in HTTP Request Smuggling**.

### Resolución

Primero identificamos que el endpoint `/resources/images/blog.svg` no valida bien el encabezado `Content-Length`, permitiendo enviar un _smuggling_ CL.0. Aprovechamos que el backend ignora el `Content-Length` en rutas válidas, pero lo respeta en las inválidas.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Creamos un grupo de peticiones con un GET camuflado tras un POST, probando con `/test` y `/prueba` para confirmar la técnica: las rutas inexistentes respetan el `Content-Length`, mientras que la ruta al recurso válido las ignora. Así verificamos la vulnerabilidad CL.0.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Luego, cambiamos la ruta inyectada de `/prueba` a `/admin` en el smuggling, para colarnos en el panel de administración.

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

Luego, cambiamos la ruta inyectada de `/prueba` a `/admin` en el smuggling, para colarnos en el panel de administración.

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1604).png" alt=""><figcaption></figcaption></figure>

## Lab: HTTP request smuggling, basic CL.TE vulnerability

### Enunciado

Este laboratorio involucra un servidor front-end y un servidor back-end, y el servidor front-end no admite codificación chunked. El servidor front-end rechaza las solicitudes que no usen el método GET o POST.

Para resolver el laboratorio, haz un ataque de request smuggling para que la siguiente solicitud procesada por el servidor back-end parezca usar el método **GPOST**.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas que solo son posibles en HTTP/1. Puedes cambiar el protocolo manualmente en Burp Repeater desde la sección de atributos de la solicitud en el panel Inspector.

**Sugerencia**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede ser complicado. Nuestra extensión HTTP Request Smuggler para Burp está diseñada para ayudar. Puedes instalarla desde la BApp Store.

### Resolución

El objetivo del lab era hacer que el back-end interpretara una solicitud con un método no permitido (en este caso **GPOST**), pero sin que el front-end la bloqueara antes. El front-end solo permitía métodos GET y POST, así que diseñamos un ataque para que la segunda petición, que empieza con **GPOST**, se “colara” al back-end.

Para lograrlo:

1. Enviamos una petición **POST** con `Transfer-Encoding: chunked` al front-end.
2. Definimos un **Content-Length** engañoso para delimitar dónde termina el cuerpo de la petición real.
3.  Insertamos el inicio de la siguiente petición en el cuerpo chunked:

    ```
    GPOST / HTTP/1.1
    Origin:
    ```

    De este modo, el front-end procesó la primera parte (el POST válido) y pasó lo restante al back-end como una petición separada.

El resultado fue que el back-end recibió y procesó **GPOST / HTTP/1.1**, que el front-end nunca habría aceptado directamente. Esto confirmó la vulnerabilidad CL.TE.

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: HTTP request smuggling, basic TE.CL vulnerability

### Enunciado

Este laboratorio involucra un servidor front-end y un servidor back-end, y el servidor back-end **no admite codificación chunked**. El servidor front-end rechaza las solicitudes que no usen el método **GET** o **POST**.

Para resolver el laboratorio, debes hacer **smuggling** de una solicitud hacia el servidor back-end, de forma que la siguiente solicitud procesada por el back-end parezca usar el método **GPOST**.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas que solo son posibles en **HTTP/1**. Puedes cambiar manualmente de protocolo en Burp Repeater desde la sección **Request attributes** del panel **Inspector**.

**Sugerencia**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede ser complicado. Nuestra extensión **HTTP Request Smuggler** para Burp está diseñada para ayudar. Puedes instalarla desde la **BApp Store**.

### Resolución

En este laboratorio el objetivo era explotar un caso de request smuggling usando TE.CL para enviar un método no permitido al back-end específicamente GPOST la aplicación del lado del front-end no soporta codificación chunked y solo acepta métodos GET o POST para evadir esta restricción construimos una petición con Transfer-Encoding chunked y un Content-Length inconsistente de modo que el front-end interpreta solo la parte chunked y considera terminada la petición mientras que el back-end lee el resto como si fuera una segunda petición con el método GPOST al enviar este payload logramos que el front-end descarte el método GPOST pero que el back-end lo lea y responda con un error diciendo que el método no está reconocido confirmando así la vulnerabilidad de request smuggling en la forma básica TE.CL.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: HTTP request smuggling, obfuscating the TE header

### Enunciado

Este laboratorio involucra un servidor front-end y un servidor back-end, y ambos manejan de forma diferente los encabezados HTTP duplicados. El servidor front-end rechaza las peticiones que no usen los métodos GET o POST.

Para resolver el laboratorio, debes hacer un ataque de request smuggling para que la siguiente petición procesada por el servidor back-end parezca usar el método GPOST.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas que solo son posibles en HTTP/1. Puedes cambiar manualmente el protocolo en Burp Repeater desde la sección de atributos de la solicitud en el panel Inspector.

**Sugerencia**\
Ajustar manualmente los campos de longitud en los ataques de request smuggling puede ser complicado. Nuestra extensión HTTP Request Smuggler para Burp fue diseñada para ayudar. Puedes instalarla desde la BApp Store.

### Resolución

Aprovechamos una diferencia en cómo el front-end y el back-end manejan encabezados duplicados enviamos dos cabeceras Transfer-Encoding una válida con chunked y otra con un valor inválido el front-end ve la inválida y descarta chunked dejando pasar la petición mientras el back-end procesa la válida como chunked con esto inyectamos un request smuggling que introduce un método GPOST en la cola del back-end consiguiendo que el servidor trate la siguiente petición como GPOST y resolviendo el laboratorio

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
