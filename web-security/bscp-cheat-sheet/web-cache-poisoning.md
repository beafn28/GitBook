# Web Cache Poisoning

## Lab 1: Poisoning con header no indexado

1. Extensión: [param-miner](https://github.com/PortSwigger/param-miner) para descubrir la cabecera.
2.

    <figure><img src="../../.gitbook/assets/image (1724).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Cabecera X-Forwarded-Host
{% endhint %}

## Lab 2: Poisoning con cookie no indexada

1. La cookie `fehost` se refleja en la respuesta del servidor pero no forma parte de la clave de caché, lo que permite modificar su valor sin invalidar la caché.
2. Se altera el valor de la cookie para romper la estructura de la respuesta e insertar un script malicioso como `alert(1)`.
3. La respuesta manipulada queda almacenada en caché (`X-Cache: hit`) y se ejecuta el payload cuando otros usuariosacceden a la página afectada.

<figure><img src="../../.gitbook/assets/image (1725).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Cabecera fehost
{% endhint %}

## Lab 3: Poisoning con múltiples headers

1. El servidor construye redirecciones usando `X-Forwarded-Scheme` y `X-Forwarded-Host`, pero no las incluye en la clave de caché.
2. Se modifican ambas cabeceras para forzar una redirección a un dominio controlado por el atacante, que carga un script JavaScript con un payload malicioso.
3. La respuesta queda cacheada (`X-Cache: hit`) y cualquier usuario que acceda al recurso recibe la versión envenenada, ejecutando el JavaScript automáticamente.

<figure><img src="../../.gitbook/assets/image (1726).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1727).png" alt=""><figcaption></figcaption></figure>

## Lab 4: Poisoning dirigido con header desconocido

1. Se identifica la cabecera no documentada `X-Host`, que el servidor usa para generar URLs de recursos estáticos sin incluirla en la clave de caché.
2. Se manipula `X-Host` para apuntar a un JavaScript alojado por el atacante con un payload como `alert(document.cookie)`, logrando que la respuesta quede cacheada.
3. Debido a `Vary: User-Agent`, se obtiene el User-Agent de la víctima mediante un comentario malicioso y se ajusta la petición para que reciba exclusivamente la versión envenenada del recurso.

<figure><img src="../../.gitbook/assets/image (1728).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1729).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1730).png" alt=""><figcaption></figcaption></figure>

Cambiar el User-agent resultante en la petición.

## Lab 5: Poisoning con query string no indexado

1. Los parámetros de la URL no se incluyen en la clave de caché, permitiendo que una respuesta generada con parámetros maliciosos se reutilice para otras peticiones.
2. Se añade un parámetro reflejado con un payload malicioso y se usa una cabecera como `Origin` para forzar la generación y almacenamiento en caché de la respuesta.
3. Al acceder a la URL sin el parámetro, la caché sigue sirviendo el contenido envenenado y el payload se ejecuta en el navegador del usuario víctima.

<figure><img src="../../.gitbook/assets/image (1723).png" alt=""><figcaption></figcaption></figure>

## Lab 6: Poisoning con parámetro de query no indexado

1. Se identifica `utm_content` como un parámetro procesado y reflejado, pero que no forma parte de la clave de caché, a diferencia del resto de la query string.
2. Se inyecta un payload malicioso en el valor de `utm_content`, logrando que la respuesta generada quede almacenada en caché.
3. Cuando un usuario accede a la página sin el parámetro, recibe la versión cacheada envenenada y se ejecuta el contenido inyectado.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Lab 7: Parameter cloaking

1. Se identifica que utm\_content es procesado por el servidor pero ignorado por la clave de caché, a diferencia del resto de parámetros. Esto habilita un escenario clásico de _parameter cloaking_.
2. Se oculta el parámetro callback dentro de utm\_content usando ;, logrando que el back-end lo interprete y genere una respuesta JavaScript maliciosa (alert(1)), que queda almacenada en caché al no variar la clave.
3. Cuando un usuario accede al recurso sin parámetros, recibe la versión cacheada envenenada y ejecuta el código inyectado, manteniéndose el ataque activo hasta que la caché sea invalidada.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-02-01 a las 21.11.28.png" alt=""><figcaption></figcaption></figure>

## Lab 8: Poisoning con petición GET anómala

1. Se aprovecha que el servidor procesa el cuerpo de una petición GET, mientras que la caché lo ignora al generar la clave, creando una discrepancia explotable (_fat GET request_).
2. El recurso /js/geolocate.js utiliza un parámetro callback definido en la URL para construir el script. Aunque ese valor sí influye en la clave de caché, el servidor también acepta una versión duplicada del parámetro en el cuerpo de la petición.
3. Al enviar alert(1) como valor del parámetro en el cuerpo del GET, el servidor genera un JavaScript malicioso que queda almacenado en caché sin alterar la clave. Cuando un usuario accede al recurso mientras la caché sigue activa, se ejecuta el payload y se completa el laboratorio.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Lab 9: Normalización de URL

1. La aplicación refleja la ruta solicitada en la respuesta, pero el navegador codifica automáticamente los caracteres especiales en la barra de direcciones, impidiendo la ejecución directa del payload y haciendo que el XSS no sea explotable por sí solo.
2. Al enviar la petición desde Burp con la carga maliciosa sin codificar en la ruta, el servidor la refleja tal cual y la respuesta se almacena en la caché, que normaliza la URL y no distingue entre la versión codificada y no codificada.
3. Cuando un usuario accede posteriormente a la misma URL desde el navegador (con la ruta codificada), la caché devuelve la respuesta previamente envenenada, provocando la ejecución exitosa de alert(1) en el navegador de la víctima.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
