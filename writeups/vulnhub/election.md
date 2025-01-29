---
description: '✍️ Autor: Love🔍 Dificultad: Intermedio📅 Fecha de creación: 02/07/2020'
---

# Election

f🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.69
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1069).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 192.168.1.69 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1071).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.69
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1072).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80. Es la página de por defecto de Apache.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-01-29 191119.png" alt=""><figcaption></figcaption></figure>



```bash
```
