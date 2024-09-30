---
description: '✍️ Autor: Maciiii__🔍 Dificultad: Fácil  📅 Fecha de creación: 19/08/2024'
---

# Pntopntobarra

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (512).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (513).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (514).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre que nos han infectado con un virus.

<figure><img src="../../.gitbook/assets/image (515).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (516).png" alt=""><figcaption></figcaption></figure>

Miramos que hay un archivo `ejemplo.php` que es el que sale cuando pulsamos en ejemplos de computadoras infectadas.&#x20;

Parece que hemos encontrado una advertencia relacionada con un posible ataque. El mensaje menciona un "LeFvIrus", que si observamos las letras en mayúscula, forma "LFI". Esto sugiere que podríamos estar lidiando con una vulnerabilidad de Inclusión de Archivos Locales (Local File Inclusion).

Al acceder a la sección "Ejemplos de computadoras infectadas", somos redirigidos a un archivo `.php` que muestra un mensaje de error: "Error al cargar el archivo". Sin embargo, en la barra de búsqueda se encuentra la siguiente URL:

```bash
http://172.17.0.2/ejemplos.php?images=./ejemplo1.png
```

Podemos experimentar cambiando el valor de `images` de `./ejemplo1.png` a `/etc/passwd`, lo que nos permitiría intentar explotar la vulnerabilidad:

```bash
http://172.17.0.2/ejemplos.php?images=/etc/passwd
```

<figure><img src="../../.gitbook/assets/image (517).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (519).png" alt=""><figcaption></figcaption></figure>

Al parecer, hemos descubierto a **Nico** que posiblemente sea un usuario, por lo que intentaremos conectarnos a través de **SSH**.

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l nico -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**.&#x20;

Obtuvimos la contraseña que es **lovely.**

<figure><img src="../../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de ese usuario. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh nico@172.17.0.2
```

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

&#x20;Observamos que tenemos permisos para ejecutar el binario `env`con privilegios de **root**. Aprovechando esta oportunidad, utilizamos el siguiente comando:

```bash
sudo env /bin/bash
```

Con esto, obtenemos acceso al **shell** como **root**, adquiriendo privilegios completos en el sistema.
