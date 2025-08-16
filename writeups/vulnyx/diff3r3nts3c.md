---
description: '🔍 Dificultad: Muy Fácil'
---

# Diff3r3ntS3c

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.64
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.64 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1082).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el **puerto 80** perteneciente al **servicio HTTP** está abierto, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 192.168.1.64
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1083).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.&#x20;

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-01-31 111248.png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Vemos que se pueden subir archivos pero con una extensión poco común que es **.phtml** por lo que subimos una Reverse Shell con esa extensión.

{% embed url="https://www.revshells.com/" %}

<figure><img src="../../.gitbook/assets/image (1088).png" alt=""><figcaption></figcaption></figure>

Revisamos directorios para ver dónde se ha subido.

```bash
gobuster dir -u http://192.168.1.64 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (1087).png" alt=""><figcaption></figcaption></figure>

Hay un directorio **/uploads** por lo que se habrá subido ahí.

<figure><img src="../../.gitbook/assets/image (1089).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

<figure><img src="../../.gitbook/assets/image (1090).png" alt=""><figcaption></figcaption></figure>

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

para ver si hay algo para explotar. No tenemos permisos SUDO.

En el directorio **/home/candidate** hay un directorio oculto y que contiene un archivo.&#x20;

<figure><img src="../../.gitbook/assets/image (1091).png" alt=""><figcaption></figcaption></figure>

Revisando el **/etc/crontab** se ejecuta cada minuto el script.

<figure><img src="../../.gitbook/assets/image (1092).png" alt=""><figcaption></figcaption></figure>

Modificamos el contenido del archivo para que cuando se ejecuta nos haga una conexión remota.

<figure><img src="../../.gitbook/assets/image (1094).png" alt=""><figcaption></figcaption></figure>

Al ponernos en escucha y recibir la conexión vemos que ya nos conectamos como root.

<figure><img src="../../.gitbook/assets/image (1093).png" alt=""><figcaption></figcaption></figure>
