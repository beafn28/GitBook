---
description: '🔍 Dificultad: Muy Fácil'
---

# Diff3r3ntS3c

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.64
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.74 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el **puerto 22** perteneciente al **servicio SS**H, el **puerto 80** perteneciente al **servicio HTTP**, el **puerto 139** y el **puerto 445**, ambos pertenecientes al **servicio SMB** (Server Message Block), están abiertos, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,139,445 -v 192.168.1.74
```

para obtener más información sobre ese puerto específicamente.
