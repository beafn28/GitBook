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
