---
description: '✍️ Autor: tryhackme, RunasRs 🔍 Dificultad: Fácil'
---

# Billing

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.111.239
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 10.10.111.239
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el puerto **22** perteneciente al servicio **SSH,** el puerto **80** perteneciente al servicio **HTTP** y el puerto **3306** perteneciente a **MySQL** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,3306 -v 10.10.111.239
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-20 213144.png" alt=""><figcaption></figcaption></figure>

Es un panel de **MagnusBilling** por lo que ahora haremos un escaneo de directorios por si hay algo relevante.

```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://10.10.111.239
```



Encontramos un directorio interesante donde nos menciona la versión y así poder encontrar vulnerabilidad.&#x20;

<div><figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-20 215053.png" alt=""><figcaption></figcaption></figure></div>

```
```
