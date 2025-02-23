---
description: '✍️ Autor: VbScrub🔍 Dificultad: Fácil 📅 Fecha de creación: 04/06/2020'
---

# Nest

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.171
```

para verificar la conectividad de red.



A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.171
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.



Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 22 (SSH):** El puerto 22 está abierto y ejecutando OpenSSH 7.6p1 en un sistema Ubuntu Linux. Se han detectado las claves del host en los algoritmos RSA, ECDSA y ED25519, lo que indica que el sistema permite conexiones seguras mediante SSH.
* **Puerto 80 (HTTP):** El puerto 80 está abierto y ejecutando un servidor web Apache 2.4.29 en Ubuntu. La página detectada es la predeterminada de Apache en Ubuntu, lo que sugiere que el servidor web podría estar recién configurado o sin contenido adicional.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.10.171
```

para que nos proporcione más información sobre esos puertos específicamente.
