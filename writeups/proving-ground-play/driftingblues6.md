---
description: '✍️ Autor: OffSec 🔍 Dificultad: Fácil'
---

# DriftingBlues6

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.138.219
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1344).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 192.168.138.219
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1345).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH,** el **puerto 631** perteneciente al **servicio IPP** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 192.168.138.219
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1346).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (1347).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente nos encontramos una recomendación.

<figure><img src="../../.gitbook/assets/image (1348).png" alt=""><figcaption></figcaption></figure>

Haciendo un escaneo de directorios.

```bash
gobuster dir -u 192.168.138.219 --wordlist /usr/share/wordlists/dirb/common.txt
```

<figure><img src="../../.gitbook/assets/image (1349).png" alt=""><figcaption></figcaption></figure>

Viendo el contenido del archivo **robots.txt** vemos que nos dice algo interesante de que hagamos un escaneo con la extensión **.zip**.

```
gobuster dir -u http://192.168.138.219 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .zip
```

