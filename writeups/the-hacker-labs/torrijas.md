---
description: '🔍 Dificultad: Principiante'
---

# Torrijas

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.66
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.66 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH y** el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,3306 -v 192.168.1.66
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 154105.png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 154309.png" alt=""><figcaption></figcaption></figure>

Ponemos el dominio **torrija.thl** en `/etc/hosts`. Tras esto realizamos un escaneo de directorios.

```
gobuster dir -u http://192.168.1.66 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x html,txt,php
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 155904.png" alt=""><figcaption></figcaption></figure>

Nos llama la atención que hay un **wordpress** por lo que indagamos más en ese directorio.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-21 160040.png" alt=""><figcaption></figcaption></figure>

Enumeramos los usuarios y los plugins con WPScan.

```
```
