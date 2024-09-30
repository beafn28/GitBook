# Nivel 0-7

### NIVEL 0

Ingresa las credenciales para el Nivel 0:

* **Usuario:** `leviathan0`
* **Contraseña:** `leviathan0`

<figure><img src="../../../.gitbook/assets/image (419).png" alt=""><figcaption></figcaption></figure>

A partir de aquí, los niveles no proporcionan instrucciones ni detalles sobre qué hacer o cómo hacerlo. Por lo tanto, solo se verán soluciones.

Al ejecutar `ls`, no se muestra ningún archivo ni directorio. Para visualizar archivos ocultos, usamos el comando `ls -la`. Observamos la presencia del directorio `.backup` y revisamos su contenido. Es probable que este directorio contenga información relevante sobre contraseñas, así que filtramos el contenido en busca de la palabra "password" utilizando `grep`.

<figure><img src="../../../.gitbook/assets/image (420).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (421).png" alt=""><figcaption></figcaption></figure>

### NIVEL 1

Ingresa las credenciales para el Nivel 1:

* **Usuario:** `leviathan1`
* **Contraseña:** `3QJ3TgzHDq`

<figure><img src="../../../.gitbook/assets/image (422).png" alt=""><figcaption></figcaption></figure>

Después de ejecutar el comando `ls`, encontramos un directorio llamado `check`. Procedemos a ejecutar `ltrace ./check`, que muestra las llamadas a librerías y las llamadas al sistema realizadas por el programa.

Al analizar la salida de `ltrace`, observamos que el programa solicita una contraseña con el prompt `"password: "` y compara la entrada con la cadena `"sex"`. Por lo tanto, la contraseña para que nos proporcionen la contraseña del siguiente nivel es `sex`.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-21 123212.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (423).png" alt=""><figcaption></figcaption></figure>

**Nota:** Al realizar el análisis del código, notarás que hay varias palabras aleatorias incluidas intencionadamente para confundir. Estas palabras no son relevantes para la solución del desafío.

### NIVEL 2

Ingresa las credenciales para el Nivel 2:

* **Usuario:** `leviathan2`
* **Contraseña:** `NsN1HwFoyN`

<figure><img src="../../../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>

Al listar los archivos, encontramos un directorio llamado `printfile`. Procedemos a ejecutar el comando `./printfile`.

<figure><img src="../../../.gitbook/assets/image (425).png" alt=""><figcaption></figcaption></figure>

Vamos a crear un directorio temporal y un nuevo archivo de texto que podamos usar para probar el programa con `ltrace`.

<figure><img src="../../../.gitbook/assets/image (252).png" alt=""><figcaption></figcaption></figure>

A partir de la salida, podemos identificar una pequeña vulnerabilidad en la manera en que funciona este programa. Si observas detenidamente, notarás que se llaman las funciones `access()` y `/bin/cat` en el archivo de entrada. La función `access()` verifica los permisos basándose en el ID de usuario real del proceso, en lugar del ID de usuario efectivo.

> **Nota:** El ID de usuario real es el propietario del proceso (quién eres realmente), mientras que el ID de usuario efectivo es lo que el sistema operativo utiliza para decidir si tienes permiso para realizar ciertas acciones. Aunque hay algunas excepciones a esta regla.

Al revisar la salida de `ls -la`, vemos que el archivo `printfile` es propiedad de `leviathan3`. Esto significa que `access()` ejecutará el proceso con los privilegios de `leviathan3`.

Además, en el código podemos ver que se utiliza `/bin/cat` para mostrar el contenido del archivo. Mientras que `access()` utiliza la ruta completa del archivo, `/bin/cat` solo utiliza la primera parte del nombre del archivo (esto se debe a la forma en que están configuradas las comillas en el programa). Podemos intentar agregar un espacio en el nombre del archivo, y si nuestra suposición es correcta, `/bin/cat` interpretará el nombre como dos archivos separados.

<figure><img src="../../../.gitbook/assets/image (253).png" alt=""><figcaption></figcaption></figure>

Como puedes observar, `/bin/cat` interpreta `"pass file.txt"` como dos archivos separados: `"pass"` y `"file.txt"`. ¡Podemos aprovechar esto! Vamos a crear un enlace simbólico para la parte `"pass"` en `"pass file.txt"` y lo vincularemos a `/etc/leviathan_pass/leviathan3`.

<figure><img src="../../../.gitbook/assets/image (254).png" alt=""><figcaption></figcaption></figure>

### NIVEL 3

Ingresa las credenciales para el Nivel 3:

* **Usuario:** `leviathan3`
* **Contraseña:** `f0n8h2iWLP`

<figure><img src="../../../.gitbook/assets/image (255).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos a nuestra disposición.

<figure><img src="../../../.gitbook/assets/image (256).png" alt=""><figcaption></figcaption></figure>

Como en el anterior nivel tenemos un ejecutable por lo que vamos a ejecutarlo a ver qué nos sale.

<figure><img src="../../../.gitbook/assets/image (257).png" alt=""><figcaption></figcaption></figure>

Paso lo mismo en el anterior nos requiere una contraseña por lo que ahora ejecutamos `ltrace` para ver que librerías llama este programa.

<figure><img src="../../../.gitbook/assets/image (258).png" alt=""><figcaption></figcaption></figure>

Al revisar la salida de `ltrace`, podemos ver que `strcmp` se llama dos veces. Esto intenta ocultarnos la contraseña principal, pero centrémonos en la que compara nuestra entrada. Vemos que intenta comparar la entrada con `"snlprintf"`; probemos usar esto como la contraseña.

<figure><img src="../../../.gitbook/assets/image (260).png" alt=""><figcaption></figcaption></figure>

### NIVEL 4

Ingresa las credenciales para el Nivel 4:

* **Usuario:** `leviathan4`
* **Contraseña:** `WG1egElCvO`

<figure><img src="../../../.gitbook/assets/image (261).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos a nuestra disposición.

<figure><img src="../../../.gitbook/assets/image (262).png" alt=""><figcaption></figcaption></figure>

Tenemos un directorio `.trash` por lo que accedemos a él para ver que contiene.

<figure><img src="../../../.gitbook/assets/image (263).png" alt=""><figcaption></figcaption></figure>

Es una salida en binario. Vamos a usar un conversor de binario a ASCII en línea. Yo utilicé esta página para hacer la conversión: [CalculadoraConversor](https://www.calculadoraconversor.com/binario-a-ascii/#google\_vignette).&#x20;

<figure><img src="../../../.gitbook/assets/image (264).png" alt=""><figcaption></figcaption></figure>

### NIVEL 5

Ingresa las credenciales para el Nivel 5:

* **Usuario:** `leviathan5`
* **Contraseña:** `0dyxT7F4QD`

<figure><img src="../../../.gitbook/assets/image (265).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos a nuestra disposición.

<figure><img src="../../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

Como podemos ver en los niveles anteriores encontramos un ejecutable llamado `leviathan5`.

<figure><img src="../../../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

Parece que el ejecutable está intentando acceder a un archivo que no existe. Vamos a ejecutar `ltrace` para ver qué se está llamando.

<figure><img src="../../../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

Parece que el ejecutable está utilizando `fopen` en `/tmp/file.log`. Vamos a crear un enlace simbólico a `/etc/leviathan_pass/leviathan6` y enlazarlo a `/tmp/file.log`.

<figure><img src="../../../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

### NIVEL 6

Ingresa las credenciales para el Nivel 6:

* **Usuario:** `leviathan6`
* **Contraseña:** `szo7HDB88w`

<figure><img src="../../../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos a nuestra disposición.

<figure><img src="../../../.gitbook/assets/image (271).png" alt=""><figcaption></figcaption></figure>

Como podemos ver en los niveles anteriores encontramos un ejecutable llamado `leviathan6`.

<figure><img src="../../../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

Este en particular nos pide ingresar un **PIN** de 4 dígitos. ¡Vamos a intentar hacer un ataque de fuerza bruta! Comenzaremos creando un directorio temporal y abriendo `nano` para escribir un script en **bash**.

<figure><img src="../../../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>

```bash
#!/bin/bash

# Ejecuta un bucle para probar todas las combinaciones de un PIN de 4 dígitos
for pin in {0000..9999}
do
    ~/leviathan6 "$pin"
done
```

Tras esperar un tiempo nos aparece la `shell`.

<figure><img src="../../../.gitbook/assets/image (274).png" alt=""><figcaption></figcaption></figure>

### NIVEL 7

Ingresa las credenciales para el Nivel 7:

* **Usuario:** `leviathan7`
* **Contraseña:** `qEs5Io5yM8`

<figure><img src="../../../.gitbook/assets/image (275).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

### 🎉✨ ¡Misión Cumplida! ✨🎉

¡Hemos conquistado todos los niveles con **éxito rotundo**! 🚀💻

