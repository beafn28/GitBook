# Nivel 11-20

### NIVEL 11

Ingresamos las credenciales para el nivel 11.

* **Usuario:** `bandit11`
* **Contraseña:** `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

<figure><img src="../../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

La contraseña para el siguiente nivel está almacenada en el archivo `data.txt`, donde todas las letras minúsculas (a-z) y mayúsculas (A-Z) han sido rotadas 13 posiciones.

<figure><img src="../../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

### NIVEL 12

Ingresamos las credenciales para el nivel 12.

* **Usuario:** `bandit12`
* **Contraseña:** `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

<figure><img src="../../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

La contraseña para el siguiente nivel está almacenada en el archivo `data.txt`, que es un volcado hexadecimal de un archivo que ha sido comprimido repetidamente. Para este nivel, puede ser útil crear un directorio bajo `/tmp` en el que puedas trabajar. Usa `mkdir` con un nombre de directorio difícil de adivinar. O mejor aún, usa el comando `mktemp -d`. Luego copia el archivo de datos utilizando `cp` y renómbralo utilizando `mv` (¡lee las páginas del manual!).

```markdown
bandit12@bandit:~$ ls
```

**Resultado:**

```kotlin
data.txt
```

#### 2. Crear un directorio temporal y copiar el archivo

```bash
bandit12@bandit:~$ mkdir /tmp/bea
bandit12@bandit:~$ cp data.txt /tmp/bea
bandit12@bandit:~$ cd /tmp/bea
bandit12@bandit:/tmp/bea$ ls
```

**Resultado:**

```kotlin
data.txt
```

#### 3. Decodificar el archivo hexadecimal

```bash
bandit12@bandit:/tmp/bea$ xxd -r data.txt > bea
bandit12@bandit:/tmp/bea$ ls
```

**Resultado:**

```kotlin
bea  data.txt
```

#### 4. Verificar el tipo de archivo

```bash
bandit12@bandit:/tmp/bea$ file bea
```

**Resultado:**

```arduino
bea: gzip compressed data, was "data2.bin", last modified: Wed Jul 17 15:57:06 2024, max compression, from Unix, original size modulo 2^32 577
```

#### 5. Renombrar y descomprimir el archivo

```bash
bandit12@bandit:/tmp/bea$ mv bea bea.gz
bandit12@bandit:/tmp/bea$ gzip -d bea.gz
bandit12@bandit:/tmp/bea$ ls
```

**Resultado:**

```kotlin
bea  data.txt
```

#### 6. Verificar nuevamente el tipo de archivo

```bash
bandit12@bandit:/tmp/bea$ file bea
```

**Resultado:**

```wasm
bea: bzip2 compressed data, block size = 900k
```

#### 7. Renombrar y descomprimir el archivo Bzip2

```bash
bandit12@bandit:/tmp/bea$ mv bea bea.bz2
bandit12@bandit:/tmp/bea$ bzip2 -d bea.bz2
bandit12@bandit:/tmp/bea$ ls
```

**Resultado:**

```kotlin
bea  data.txt
```

#### 8. Verificar nuevamente el tipo de archivo

```bash
bandit12@bandit:/tmp/bea$ file bea
```

**Resultado:**

```arduino
bea: gzip compressed data, was "data4.bin", last modified: Wed Jul 17 15:57:06 2024, max compression, from Unix, original size modulo 2^32 20480
```

#### 9. Repetir los pasos de renombrar y descomprimir

```bash
bandit12@bandit:/tmp/bea$ mv bea bea.gz
bandit12@bandit:/tmp/bea$ gzip -d bea.gz
bandit12@bandit:/tmp/bea$ ls
```

**Resultado:**

```kotlin
bea  data.txt
```

#### 10. Verificar nuevamente el tipo de archivo

```bash
bandit12@bandit:/tmp/bea$ file bea
```

**Resultado:**

```java
bea: POSIX tar archive (GNU)
```

#### 11. Extraer el archivo TAR

```bash
bandit12@bandit:/tmp/bea$ mv bea bea.tar
bandit12@bandit:/tmp/bea$ tar xf bea.tar
bandit12@bandit:/tmp/bea$ ls
```

**Resultado:**

```kotlin
bea.tar  data5.bin  data.txt
```

#### 12. Continuar descomprimiendo hasta encontrar un archivo ASCII

```bash
bandit12@bandit:/tmp/bea$ file data5.bin
```

**Resultado:**

```java
data5.bin: POSIX tar archive (GNU)
```

Repite los pasos de descompresión y extracción según el tipo de archivo detectado. En este caso:

```bash
bandit12@bandit:/tmp/bea$ mv data5.bin bea.tar
bandit12@bandit:/tmp/bea$ tar xf bea.tar
bandit12@bandit:/tmp/bea$ ls
bandit12@bandit:/tmp/bea$ file data6.bin
```

**Resultado:**

```wasm
data6.bin: bzip2 compressed data, block size = 900k
```

Descomprime y repite hasta obtener:

```bash
bandit12@bandit:/tmp/bea$ file bea
```

**Resultado:**

```vbnet
bea: ASCII text
```

#### 13. Leer el archivo final para obtener la contraseña

```bash
bandit12@bandit:/tmp/bea$ cat bea
```

**Resultado:**

```csharp
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

### NIVEL 13

Ingresamos las credenciales para el nivel 13.

* **Usuario:** `bandit13`
* **Contraseña:** `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

<figure><img src="../../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

La contraseña para el siguiente nivel está almacenada en `/etc/bandit_pass/bandit14` y solo puede ser leída por el usuario `bandit14`. En este nivel, no obtienes la contraseña directamente, pero recibes una clave SSH privada que puede ser utilizada para iniciar sesión en el siguiente nivel. Nota: `localhost` es un nombre de host que se refiere a la máquina en la que estás trabajando.

<figure><img src="../../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

La contraseña es `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

### NIVEL 14

Ingresamos las credenciales para el nivel 14.

* **Usuario:** `bandit14`
* **Contraseña:** `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

Para resolver este nivel, debes enviar la contraseña del nivel actual (la que ya has obtenido) al puerto 30000 en `localhost` y recibir la respuesta que será la contraseña para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

La contraseña es `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

### NIVEL 15

Ingresamos las credenciales para el nivel 15.

* **Usuario:** `bandit15`
* **Contraseña:** `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

<figure><img src="../../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

Para este nivel, necesitas enviar la contraseña del nivel actual al puerto 30001 en `localhost` utilizando una conexión cifrada con SSL/TLS. La herramienta que utilizarás es `openssl`, específicamente el comando `s_client`, que permite establecer una conexión SSL/TLS con un servidor.

*   **Establecer una conexión SSL/TLS con el puerto 30001 en localhost**:

    ```bash
    ssl s_client -connect localhost:30001
    ```

    Esto establecerá una conexión cifrada con el puerto 30001 en `localhost`.
*   **Enviar la contraseña del nivel actual**:

    Una vez que la conexión esté establecida, escribe la contraseña del nivel actual y presiona `Enter`.

<figure><img src="../../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

La contraseña es `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

### NIVEL 16

Ingresamos las credenciales para el nivel 16.

* **Usuario:** `bandit16`
* **Contraseña:** `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

<figure><img src="../../../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

Para resolver este nivel, debes realizar dos pasos principales: identificar los puertos en el rango 31000-32000 que están escuchando en `localhost`, y luego determinar cuál de esos puertos utiliza SSL/TLS y te proporciona las credenciales para el siguiente nivel.

<figure><img src="../../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

Para cada puerto que `nmap` haya identificado como abierto, necesitarás verificar si está utilizando SSL/TLS y si te proporciona las credenciales correctas.

<figure><img src="../../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

Se sale de la sesión de bandit16 y sea crea un archivo con la clave RSA y se conecta con bandit 17 de esta manera. Se dan permisos 400 al archivo con la clave.

<figure><img src="../../../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

### NIVEL 17

Ingresamos las credenciales para el nivel 17.

* **Usuario:** `bandit17`

En el directorio de inicio hay 2 archivos: `passwords.old` y `passwords.new`. La contraseña para el siguiente nivel está en `passwords.new` y es la única línea que ha sido cambiada entre `passwords.old` y `passwords.new`.

> **Nota:** Si has resuelto este nivel y ves el mensaje **"Byebye!"** al intentar iniciar sesión en `bandit18`, esto está relacionado con el siguiente nivel, `bandit19`.

La salida del comando `diff` muestra que la única línea que ha cambiado entre `passwords.old` y `passwords.new` es la línea que contiene la contraseña para el siguiente nivel.

Aquí está la diferencia:

```diff
42c42
< bSrACvJvvBSxEM2SGsV5sn09vc3xgqyp
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

### NIVEL 18

Ingresamos las credenciales para el nivel 18.

* **Usuario:** `bandit18`
* **Contraseña:** `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

<figure><img src="../../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

La contraseña para el siguiente nivel está almacenada en un archivo llamado `readme` en el directorio de inicio. Desafortunadamente, alguien ha modificado el archivo `.bashrc` para desconectarte automáticamente cuando inicies sesión mediante SSH.

<figure><img src="../../../.gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>

### NIVEL 19

Ingresamos las credenciales para el nivel 19.

* **Usuario:** `bandit19`
* **Contraseña:** `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

<figure><img src="../../../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>

Para acceder al siguiente nivel, debes utilizar el binario `setuid` que se encuentra en el directorio de inicio. Ejecútalo sin argumentos para descubrir cómo utilizarlo. La contraseña para este nivel se encuentra en el lugar habitual (`/etc/bandit_pass`), después de haber usado el binario `setuid`.

<figure><img src="../../../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>

### NIVEL 20

Ingresamos las credenciales para el nivel 20.

* **Usuario:** `bandit20`
* **Contraseña:** `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

<figure><img src="../../../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

Hay un binario con el bit `setuid` en el directorio de inicio que hace lo siguiente: se conecta a `localhost` en el puerto que especifiques como argumento de línea de comandos. Luego lee una línea de texto de la conexión y la compara con la contraseña del nivel anterior (bandit20). Si la contraseña es correcta, transmitirá la contraseña para el siguiente nivel (bandit21).

> NOTA: Intenta conectarte a tu propio daemon de red para ver si funciona como piensas.

*   **Escuchar en un Puerto con `nc`**

    ```bash
    nc -l -p 4444
    ```
*   **Ejecutar `suconnect` Especificando el Puerto**

    ```bash
    ./suconnect 4444
    ```
* **Ingresar la Contraseña del Nivel Anterior en la Terminal de nc.**

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-23 171845.png" alt=""><figcaption></figcaption></figure>

&#x20;
