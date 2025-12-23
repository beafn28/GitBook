# Web Requests

{% file src="../.gitbook/assets/Web_Requests_Module_Cheat_Sheet.pdf" %}

## **Fundamentos HTTP**

[HTTP](https://tools.ietf.org/html/rfc2616) es un protocolo a nivel de aplicación utilizado para acceder a los recursos de la World Wide Web. El término `hypertext`representa un texto que contiene enlaces a otros recursos y texto que los lectores pueden interpretar fácilmente.

La comunicación HTTP consiste en un cliente y un servidor, donde el cliente solicita al servidor un recurso. El servidor procesa las solicitudes y devuelve el recurso solicitado. El puerto por defecto para la comunicación HTTP es el puerto `80`, aunque esto se puede cambiar a cualquier otro puerto, dependiendo de la configuración del servidor web. Las mismas solicitudes se utilizan cuando utilizamos Internet para visitar diferentes sitios web.

### URL

<figure><img src="../.gitbook/assets/url_structure.webp" alt=""><figcaption></figcaption></figure>

| `Scheme`       | `http://` `https://` | Esto se utiliza para identificar el protocolo al que se accede el cliente, y termina con un colon y un doble corte (`://`)                                                                                                |
| -------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `User Info`    | `admin:password@`    | Este es un componente opcional que contiene las credenciales (separadas por un colon `:`) acostumbrado a autenticar al huésped, y se separa del huésped con un signo de señal (`@`)                                       |
| `Host`         | `inlanefreight.com`  | El anfitrión significa la ubicación del recurso. Esto puede ser un nombre de host o una dirección IP                                                                                                                      |
| `Port`         | `:80`                | El `Port`está separado de la `Host`por un colon (`:`). Si no se especifica ningún puerto, `http`sistemas por defecto a puerto `80`y `https`Fallo a puerto `443`                                                           |
| `Path`         | `/dashboard.php`     | Esto apunta al recurso al que se accede, que puede ser un archivo o una carpeta. Si no hay una ruta especificada, el servidor devuelve el índice predeterminado (por ejemplo. `index.html`).                              |
| `Query String` | `?login=true`        | La cadena de consulta comienza con un signo de interrogación (`?`), y consiste en un parámetro (por ejemplo. `login`) y un valor (por ejemplo. `true`). Múltiples parámetros pueden ser separados por un ampersand (`&`). |
| `Fragments`    | `#status`            | Los fragmentos son procesados por los navegadores del lado del cliente para localizar secciones dentro del recurso primario (por ejemplo, una cabecera o sección en la página).                                           |

{% hint style="warning" %}
No todos los componentes están obligados a acceder a un recurso. Los principales campos obligatorios son el scheme y el host, sin los cuales la solicitud no tendría recurso para solicitar.
{% endhint %}

### Funcionamiento HTTP

<figure><img src="../.gitbook/assets/HTTP_Flow.webp" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Nuestros navegadores suelen buscar los registros en el local `/etc/hosts`, y si el dominio solicitado no existe dentro de él, entonces se pondrían en contacto con otros servidores DNS. Podemos usar el `/etc/hosts` para agregar manualmente registros para la resolución DNS, añadiendo la IP seguida por el nombre de dominio.
{% endhint %}

### cURL

Es una herramienta de línea de comandos y biblioteca que soporta principalmente HTTP junto con muchos otros protocolos. Esto lo convierte en un buen candidato para scripts, así como automatización, por lo que es esencial para enviar varios tipos de solicitudes web desde la línea de comandos, que es necesaria para muchos tipos de pruebas de penetración web.

```
curl inlanefreight.com
```

También podemos descargar el contenido con el parámetro `-O` y para redirigirlo con un nombre específico `-o`.

```
curl -O inlanefreight.com/index.html
```

Con `--help-all` podemos ver todos los parámetros disponibles.

#### Preguntas

**To get the flag, start the above exercise, then use cURL to download the file returned by '/download.php' in the server shown above.**

```
curl 94.237.120.230:39753/download.php
```

### Hypertext Transfer Protocol Secure (HTTPS)

En la sección anterior, discutimos cómo se envían y procesan las solicitudes HTTP. Sin embargo, uno de los inconvenientes significativos de HTTP es que todos los datos se transfieren en texto claro. Esto significa que cualquier persona entre la fuente y el destino puede realizar un ataque Man-In-The-Middle para ver los datos transferidos.

Para contrarrestar este problema, se creó el [protocolo HTTPS (HTTP Secure),](https://tools.ietf.org/html/rfc2660) en el que todas las comunicaciones se transfieren en un formato cifrado, por lo que incluso si un tercero intercepta la solicitud, no serían capaces de extraer los datos de ella. Por esta razón, HTTPS se ha convertido en el esquema principal para sitios web en Internet, y HTTP está siendo eliminado gradualmente, y pronto la mayoría de los navegadores web no permitirán visitar sitios web HTTP.

{% hint style="warning" %}
Aunque los datos transferidos a través del protocolo HTTPS pueden ser cifrados, la solicitud todavía puede revelar la URL visitada si se puso en contacto con un servidor DNS de texto claro. Por esta razón, se recomienda utilizar servidores DNS cifrados (por ejemplo. 8.8.8.8 o 1.1.1.1), o utilizar un servicio VPN para asegurar que todo el tráfico esté debidamente cifrado.
{% endhint %}

<figure><img src="../.gitbook/assets/HTTPS_Flow.webp" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Dependiendo de las circunstancias, un atacante puede ser capaz de realizar un ataque de degradación HTTP, que degrada la comunicación HTTPS a HTTP, haciendo que los datos sean transferidos en texto claro. Esto se hace mediante la configuración de un representante de Man-In-The-Middle (MITM) para transferir todo el tráfico a través del host del atacante sin el conocimiento del usuario. Sin embargo, la mayoría de los navegadores, servidores y aplicaciones web modernos protegen contra este ataque.
{% endhint %}

### cURL en HTTPS

Por defecto, **cURL** bloquea la conexión si el certificado SSL es inválido para evitar ataques MITM.\
Si queremos saltar esa verificación (por ejemplo, en entornos de prueba con certificados no válidos), usamos la opción:

```
curl -k https://inlanefreight.com
```

Esto permite la conexión ignorando el error del certificado.

### Peticiones y respuestas de HTTP

#### Petición HTTP

<figure><img src="../.gitbook/assets/raw_request.webp" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
HTTP versión 1. X envía solicitudes como texto claro, y utiliza un carácter de nueva línea para separar diferentes campos y diferentes solicitudes. Versión HTTP 2. X, por otro lado, envía solicitudes como datos binarios en forma de diccionario.
{% endhint %}

#### Respuesta HTTP

<figure><img src="../.gitbook/assets/raw_response.webp" alt=""><figcaption></figcaption></figure>

#### cURL

También podemos ver la petición y la respuesta completa con el parámetro `-v`.

{% hint style="warning" %}
Para más verbosidad -vvv.
{% endhint %}

#### DevTools

<figure><img src="../.gitbook/assets/devtools_network_requests.jpg" alt=""><figcaption></figcaption></figure>

#### Preguntas

**What is the HTTP method used while intercepting the request? (case-sensitive)**

```
curl 94.237.120.230:39753 -v
```

**Send a GET request to the above server, and read the response headers to find the version of Apache running on the server, then submit it as the answer. (answer format: X.Y.ZZ)**

```
curl 94.237.120.230:39753 -v
```

### Encabezados HTTP

<table><thead><tr><th>Tipo de Header</th><th width="178.3333740234375">Header</th><th width="249">Ejemplo</th><th>Descripción</th></tr></thead><tbody><tr><td><strong>General</strong></td><td>Date</td><td><code>Date: Wed, 16 Feb 2022 10:38:44 GMT</code></td><td>Fecha y hora de origen del mensaje en formato UTC.</td></tr><tr><td></td><td>Connection</td><td><code>Connection: close</code></td><td>Indica si mantener (<code>keep-alive</code>) o cerrar (<code>close</code>) la conexión tras la solicitud.</td></tr><tr><td><strong>Entity</strong></td><td>Content-Type</td><td><code>Content-Type: text/html</code></td><td>Tipo de recurso transferido; puede incluir <code>charset</code> (ej. UTF-8).</td></tr><tr><td></td><td>Media-Type</td><td><code>Media-Type: application/pdf</code></td><td>Similar a Content-Type, describe el tipo de dato transferido.</td></tr><tr><td></td><td>Boundary</td><td><code>boundary="b4e4fbd93540"</code></td><td>Separador entre partes de un mensaje multipart.</td></tr><tr><td></td><td>Content-Length</td><td><code>Content-Length: 385</code></td><td>Tamaño del contenido en bytes.</td></tr><tr><td></td><td>Content-Encoding</td><td><code>Content-Encoding: gzip</code></td><td>Codificación aplicada al contenido (ej. compresión).</td></tr><tr><td><strong>Request</strong></td><td>Host</td><td><code>Host: www.inlanefreight.com</code></td><td>Host solicitado (dominio o IP). Importante para virtual hosting.</td></tr><tr><td></td><td>User-Agent</td><td><code>User-Agent: curl/7.77.0</code></td><td>Identifica el cliente (navegador, versión, SO).</td></tr><tr><td></td><td>Referer</td><td><code>Referer: http://www.inlanefreight.com/</code></td><td>Página desde la que proviene la solicitud.</td></tr><tr><td></td><td>Accept</td><td><code>Accept: */*</code></td><td>Tipos de contenido que acepta el cliente.</td></tr><tr><td></td><td>Cookie</td><td><code>Cookie: PHPSESSID=b4e4fbd93540</code></td><td>Datos de sesión u otra información almacenada en cookies.</td></tr><tr><td></td><td>Authorization</td><td><code>Authorization: BASIC cGFzc3dvcmQK</code></td><td>Credenciales o tokens para autenticación.</td></tr><tr><td><strong>Response</strong></td><td>Server</td><td><code>Server: Apache/2.2.14 (Win32)</code></td><td>Información sobre el servidor HTTP y versión.</td></tr><tr><td></td><td>Set-Cookie</td><td><code>Set-Cookie: PHPSESSID=b4e4fbd93540</code></td><td>Cookies enviadas por el servidor al cliente.</td></tr><tr><td></td><td>WWW-Authenticate</td><td><code>WWW-Authenticate: BASIC realm="localhost"</code></td><td>Tipo de autenticación requerida por el servidor.</td></tr><tr><td><strong>Security</strong></td><td>Content-Security-Policy</td><td><code>Content-Security-Policy: script-src 'self'</code></td><td>Define políticas de carga de recursos, previniendo ataques XSS.</td></tr><tr><td></td><td>Strict-Transport-Security</td><td><code>Strict-Transport-Security: max-age=31536000</code></td><td>Obliga a usar HTTPS y evita HTTP en el dominio.</td></tr><tr><td></td><td>Referrer-Policy</td><td><code>Referrer-Policy: origin</code></td><td>Controla el envío de la cabecera Referer para evitar filtrado de información.</td></tr></tbody></table>

{% embed url="https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers" %}

#### cURL

| Parámetro            | Función                                                                    | Nota                                                                    |
| -------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `-v`                 | Muestra todos los detalles de la solicitud y respuesta (verbose).          | Incluye cabeceras y datos.                                              |
| `-I`                 | Envía una solicitud **HEAD** y muestra solo las cabeceras de la respuesta. | No descarga el cuerpo.                                                  |
| `-i`                 | Muestra **cabeceras + cuerpo** en cualquier solicitud.                     | A diferencia de `-I`, sí envía la solicitud completa (GET, POST, etc.). |
| `-H "Header: valor"` | Añade o modifica una cabecera personalizada en la solicitud.               | Útil para pruebas.                                                      |
| `-A "User-Agent"`    | Cambia el **User-Agent**.                                                  | Atajo para `-H "User-Agent: ..."`.                                      |
| `-b "name=value"`    | Envía cookies al servidor.                                                 | Atajo para `-H "Cookie: ..."`.                                          |

#### DevTools

<figure><img src="../.gitbook/assets/devtools_network_requests_details.jpg" alt=""><figcaption></figcaption></figure>

#### Preguntas

**The server above loads the flag after the page is loaded. Use the Network tab in the browser devtools to see what requests are made by the page, and find the request to the flag.**

Buscamos la página y la volvemos a cargar.

<figure><img src="../.gitbook/assets/image (1678).png" alt=""><figcaption></figcaption></figure>

## Métodos HTTP

### Métodos HTTP y Códigos de Estado

| Método      | Descripción                                                                                                                                                                                         |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **GET**     | Solicita un recurso específico. Puede enviar datos adicionales al servidor mediante _query strings_ en la URL (`?param=value`).                                                                     |
| **POST**    | Envía datos al servidor (texto, PDFs, binarios, formularios, archivos). Los datos van en el **cuerpo de la solicitud** después de las cabeceras. Usado en formularios, logins o subida de ficheros. |
| **HEAD**    | Solicita solo las cabeceras que retornaría un GET, sin el cuerpo. Útil para comprobar tamaño o metadatos antes de descargar.                                                                        |
| **PUT**     | Crea nuevos recursos en el servidor. Si no está controlado, puede permitir subir ficheros maliciosos.                                                                                               |
| **DELETE**  | Elimina un recurso existente. Si está inseguro, puede causar DoS borrando archivos críticos.                                                                                                        |
| **OPTIONS** | Devuelve información sobre el servidor, como los métodos HTTP que acepta.                                                                                                                           |
| **PATCH**   | Aplica modificaciones parciales a un recurso.                                                                                                                                                       |

{% hint style="warning" %}
La disponibilidad de métodos depende del servidor y configuración de la aplicación. En aplicaciones REST, **PUT** y **DELETE** son comunes para actualizar y eliminar datos en endpoints.
{% endhint %}

#### Códigos de Estado

| Clase   | Descripción                                                                        |
| ------- | ---------------------------------------------------------------------------------- |
| **1xx** | Información, no afecta al procesamiento.                                           |
| **2xx** | Solicitud exitosa.                                                                 |
| **3xx** | Redirección del cliente a otro recurso.                                            |
| **4xx** | Error en la solicitud del cliente (malformada, recurso inexistente, sin permisos). |
| **5xx** | Error interno del servidor al procesar la solicitud.                               |

{% embed url="https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status" %}

{% hint style="warning" %}
Algunos servidores tiene sus propios códigos de estado.
{% endhint %}

### GET

#### HTTP Basic Auth y cURL

| Acción                                           | Comando cURL                                                          | Descripción                                                                                 |
| ------------------------------------------------ | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Ver solo cabeceras y estado (no autenticado)** | `curl -i http://<IP>:<PORT>/`                                         | Muestra cabeceras y cuerpo, retornará `401 Authorization Required` si requiere auth básica. |
| **Autenticarse con usuario y pass**              | `curl -u admin:admin http://<IP>:<PORT>/`                             | Envía credenciales con Basic Auth.                                                          |
| **Autenticarse en URL**                          | `curl http://admin:admin@<IP>:<PORT>/`                                | Incluye usuario:pass en la URL.                                                             |
| **Autenticarse con Authorization Header**        | `curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<IP>:<PORT>/` | Usa valor Base64 de `usuario:contraseña`.                                                   |
| **Ver solicitud y respuesta en detalle**         | `curl -v http://admin:admin@<IP>:<PORT>/`                             | Muestra headers enviados y recibidos, incluyendo `Authorization`.                           |

#### GET Parameters

* **GET** envía parámetros en la URL (`?param=valor`).
* Se pueden capturar fácilmente en **DevTools → Network** para entender cómo interactúa el front con el backend.
* Desde DevTools se puede copiar una solicitud:
  * **Copy as cURL** → pegar en terminal para reproducirla.
  * **Copy as Fetch** → pegar en consola JS del navegador para ejecutarla.

**Ejemplo cURL con parámetro y auth:**

```
curl 'http://<IP>:<PORT>/search.php?search=le' \
-H 'Authorization: Basic YWRtaW46YWRtaW4='
```

#### Preguntas

The exercise above seems to be broken, as it returns incorrect results. Use the browser devtools to see what is the request it is sending when we search, and use cURL to search for 'flag' and obtain the flag.

```
curl 'http://admin:admin@94.237.58.104:55180/search.php?search=flag' -H 'Authorization: Basic YWRtaW46YWRtaW4='
```

### POST

Diferencia con GET:

* Parámetros en el cuerpo de la solicitud, no en la URL.
* Ventajas:
  1. No se registran en logs como la URL (mejor para datos grandes).
  2. Acepta binarios y menos necesidad de codificación.
  3. Permite enviar más datos (URL < \~2000 caracteres).

#### Ejemplos con cURL

#### Ejemplos con cURL

#### 1. Envío de formulario (login)

```
curl -X POST -d 'username=admin&password=admin' http://<IP>:<PORT>/
```

`-X POST` → Método POST\
`-d` → Datos a enviar en el cuerpo\
Si hay redirección después de login → usar -L

#### 2. Ver cookie autenticada

```
curl -X POST -d 'username=admin&password=admin' -i http://<IP>:<PORT>/
```

`-i` → Muestra cabeceras (incluye `Set-Cookie`)

#### 3. Reutilizar cookie autenticada

```
curl -b 'PHPSESSID=valor_cookie' http://<IP>:<PORT>/
# o
curl -H 'Cookie: PHPSESSID=valor_cookie' http://<IP>:<PORT>/
```

#### POST con JSON

Ejemplo de búsqueda usando Content-Type: application/json:

```
curl -X POST -d '{"search":"london"}' \
-b 'PHPSESSID=valor_cookie' \
-H 'Content-Type: application/json' \
http://<IP>:<PORT>/search.php
```

`-H 'Content-Type: application/json'` → necesario para enviar JSON\
Sin cookie o sin Content-Type correcto → la app puede no responder como esperado

#### Preguntas

**Obtain a session cookie through a valid login, and then use the cookie with cURL to search for the flag through a JSON POST request to '/search.php'**

Primero vemos la cookie tras iniciar sesión el la parte de Storage en DevTools. Tras esto realizamos la petición.

```
curl -X POST -d '{"search":"flag"}' \
-b 'PHPSESSID=b11911m54lsk13n9n42ff5a9v4' \
-H 'Content-Type: application/json' \
http://94.237.54.192:58065/search.php
```

### CRUD API

* **CRUD**: Create (POST), Read (GET), Update (PUT), Delete (DELETE) sobre entidades de base de datos.
* Muchas APIs permiten especificar tabla y fila directamente en la URL (`/api.php/city/london`).
* Respuestas suelen ir en **JSON**.
* Autenticación puede requerir cookies o cabeceras (Authorization, JWT, etc.).

#### Operaciones CRUD

**Read (GET)**

```
curl http://<IP>:<PORT>/api.php/city/london
curl -s http://<IP>:<PORT>/api.php/city/london | jq
```

* `-s` para salida silenciosa
* `| jq` para formatear JSON

Obtener coincidencias o todos los registros:

```
curl -s http://<IP>:<PORT>/api.php/city/le | jq
curl -s http://<IP>:<PORT>/api.php/city/ | jq
```

**Create (POST)**

```
curl -X POST http://<IP>:<PORT>/api.php/city/ \
-d '{"city_name":"HTB_City", "country_name":"HTB"}' \
-H 'Content-Type: application/json'
```

Verificar creación:

```
curl -s http://<IP>:<PORT>/api.php/city/HTB_City | jq
```

**Update (PUT)**

```
curl -X PUT http://<IP>:<PORT>/api.php/city/london \
-d '{"city_name":"New_HTB_City", "country_name":"HTB"}' \
-H 'Content-Type: application/json'
```

Verificar cambio:

```
curl -s http://<IP>:<PORT>/api.php/city/New_HTB_City | jq
```

**Delete (DELETE)**

```
curl -X DELETE http://<IP>:<PORT>/api.php/city/New_HTB_City
```

Confirmar borrado:

```
curl -s http://<IP>:<PORT>/api.php/city/New_HTB_City | jq
```

#### Preguntas

First, try to update any city's name to be 'flag'. Then, delete any city. Once done, search for a city named 'flag' to get the flag.

```
curl -s http://http://94.237.60.55:56227/api.php/city/ | jq

curl -X PUT http://94.237.60.55:56227/api.php/city/london \
-d '{"city_name":"flag", "country_name":"(UK)"}' \
-H 'Content-Type: application/json'

curl -X DELETE http://94.237.60.55:56227/api.php/city/Liverpool

curl -s http://http://94.237.60.55:56227/api.php/city/flag
```
