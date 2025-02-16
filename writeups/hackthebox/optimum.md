---
description: '✍️ Autor: ch4p 🔍 Dificultad: Fácil 📅 Fecha de creación: 3/10/2017'
---

# Optimum

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.8
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-16 183702.png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.8
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-16 184014.png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 80 (HTTP):** Este puerto generalmente está asociado a servidores web. Si el servicio está ejecutando un servidor web como Apache, Nginx o IIS, es recomendable buscar vulnerabilidades conocidas relacionadas con el software y versiones en uso (por ejemplo, a través de CVEs). Además, es importante realizar pruebas de seguridad en las aplicaciones web que se ejecutan en el servidor, como inyecciones SQL, Cross-Site Scripting (XSS) o vulnerabilidades de escalada de privilegios.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 10.10.10.8
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-16 185051.png" alt=""><figcaption></figcaption></figure>

Buscamos exploits sobre esa versión.

```bash
searchsploit HFS 2.3
```

<figure><img src="../../.gitbook/assets/image (1237).png" alt=""><figcaption></figcaption></figure>

Explotamos a través de Metasploit.

cd C:\Users\Administrator\Desktop

