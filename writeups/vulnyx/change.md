---
description: '🔍 Dificultad: Media'
---

# Change

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.76
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1145).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.76 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.
