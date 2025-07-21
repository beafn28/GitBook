---
description: '🔍 Dificultad: Media'
---

# Controler

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.50
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1655).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.50
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-21 162504.png" alt=""><figcaption></figcaption></figure>

