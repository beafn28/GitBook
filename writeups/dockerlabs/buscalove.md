---
description: '✍️ Autor: Prendelo  🔍 Dificultad: Fácil  📅 Fecha de creación: 31/05/2024'
---

# BuscaLove

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.21.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.21.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (6).png>)

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.21.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/2 (6).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea **Apache2**.

![](<../../.gitbook/assets/3 (6).png>)

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.21.0.2/ -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 
```

<figure><img src="../../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un directorio **WordPress**, por lo que indagaremos en él, aunque no encontraremos nada relevante en el código fuente. Hacemos un escaneo de los archivos que se encuentren en ese directorio con:

```bash
gobuster dir -u http://172.21.0.2/ -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 20 -x html,php,txt,js
```

![](<../../.gitbook/assets/5 (6).png>)

![](<../../.gitbook/assets/6 (5).png>)

Aquí solo sabemos que el archivo principal es **index.php**. Vamos a utilizar **wfuzz** para realizar un análisis de fuzzing y determinar si la página web es vulnerable a **Local File Inclusion (LFI)** con el comando:

```bash
wfuzz -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.21.0.2/wordpress/index.php?FUZZ=../../../../../../../etc/passwd --hc 404 --hl 40
```

La página web tiene una vulnerabilidad **LFI** que permite acceder a archivos locales del servidor. Al usar **Path Traversal** y escribir:

```bash
?love=../../../../../../etc/passwd
```

en la URL, puedes ver el contenido del archivo `/etc/passwd`, que contiene información sobre los usuarios del sistema.\


<figure><img src="../../.gitbook/assets/7 (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/8 (5).png" alt=""><figcaption></figcaption></figure>

Encontramos a los usuarios **Rosa** y **Pedro** por lo que mediante fuerza bruta intentaremos sacar la contraseña del **SSH**.

### 🚀 EXPLOTACIÓN

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l rosa -P /usr/share/wordlists/rockyou.txt 172.21.0.2 ssh -t 50
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.21.0.2**. Hemos probado con **Pedro**, pero con quien da respuesta es con el usuario **rosa**.

![](<../../.gitbook/assets/9 (5).png>)

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **rosa**, sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh rosa@172.21.0.2
```

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

![](<../../.gitbook/assets/10 (4).png>)

<figure><img src="../../.gitbook/assets/11 (3).png" alt=""><figcaption></figcaption></figure>

Dado que tenemos permisos para usar **sudo** con `ls` y `cat`, podemos explorar cualquier directorio del sistema y acceder a los archivos. Vamos a comenzar enumerando el directorio del usuario **root** para buscar archivos o configuraciones interesantes. Además, podemos leer el archivo `/etc/shadow` para obtener los hashes de las contraseñas de los usuarios y, si es necesario, intentar crackearlos utilizando una wordlist como **rockyou.txt**.

![](<../../.gitbook/assets/12 (3).png>)

Encontramos un archivo llamado **secret.txt** y ahora mostraremos qué contiene con el comando:

```bash
sudo cat /root/secret.txt
```

\
El contenido se encuentra en formato hexadecimal por lo que tocará decodificarlo con esta página [CyberChef](https://gchq.github.io/CyberChef/).

<figure><img src="../../.gitbook/assets/13 (2).png" alt=""><figcaption></figcaption></figure>

![](<../../.gitbook/assets/14 (1).png>)

Observamos que es una contraseña, por lo que intentamos loguear con el usuario **Pedro**. Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

![](<../../.gitbook/assets/15 (1).png>)

Podemos usar **env** para ejecutar una shell con permisos de **root**.

<figure><img src="../../.gitbook/assets/image (524).png" alt=""><figcaption></figcaption></figure>

