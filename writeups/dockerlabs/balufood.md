---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  29/04/2025
---

# BaluFood

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el **puerto 22** correspondiente al servicio **SSH** y el **puerto 5000**, comúnmente utilizado por **aplicaciones web en desarrollo**, se encuentran abiertos, por lo que a continuación se indagará más sobre ambos servicios, intentando establecer una conexión remota vía SSH y acceder mediante el navegador al servicio web en el puerto 5000 para identificar posibles vectores de ataque.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,5000 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 5000.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Es una página web de un restaurante e indagando un poco vemos que tenemos un panel de login y además nos podemos loguear con credenciales por defecto (admin:admin).

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Viendo el código fuente nos encontramos unas credenciales por lo que nos conectamos por **SSH**.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

```bash
ssh sysadmin@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

No tenemos permisos sudo pero en su directorio principal si listamos y mostramos el contenido de
