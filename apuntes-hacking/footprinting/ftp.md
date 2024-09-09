# FTP

El **Protocolo de Transferencia de Archivos (FTP)** es uno de los protocolos más antiguos de Internet. FTP opera en la **capa de aplicación** del modelo **TCP/IP**, al igual que **HTTP** o **POP**. Estos protocolos suelen funcionar con el soporte de navegadores o clientes de correo electrónico. También existen programas especiales de FTP para utilizar este protocolo.

Imaginemos que queremos **subir archivos locales** a un servidor y **descargar otros archivos** utilizando FTP. En una conexión FTP, se abren dos canales:

1. **Canal de control**: Primero, el cliente y el servidor establecen un canal de control a través del puerto **TCP 21**. El cliente envía comandos al servidor, y el servidor devuelve códigos de estado.
2. **Canal de datos**: Posteriormente, ambos establecen un canal de datos a través del puerto **TCP 20** para la transmisión exclusiva de datos. Este canal controla los errores durante la transferencia y permite reanudar la transmisión en caso de que la conexión se interrumpa.

**Modos de FTP: Activo y Pasivo**

* **FTP Activo**: El cliente establece la conexión a través del puerto TCP 21 e informa al servidor sobre qué puerto puede transmitir sus respuestas. Si el cliente está detrás de un **firewall**, el servidor no puede responder porque las conexiones externas están bloqueadas.
* **FTP Pasivo**: El servidor anuncia un puerto a través del cual el cliente puede establecer el canal de datos. Dado que el cliente inicia la conexión, el firewall no bloquea la transferencia.

**Comandos y Códigos de Estado en FTP**

FTP utiliza diversos **comandos** y **códigos de estado** para comunicar acciones y resultados entre el cliente y el servidor. Algunos ejemplos incluyen la descarga y subida de archivos, organización de directorios, o eliminación de archivos.

**Seguridad en FTP**

FTP es un protocolo en **texto claro**, lo que significa que la información puede ser interceptada si las condiciones de la red lo permiten. También existe la posibilidad de utilizar **FTP anónimo**, donde los usuarios pueden subir o descargar archivos sin autenticación. Sin embargo, esto tiene riesgos de seguridad, por lo que las opciones suelen ser limitadas en este tipo de servidores.

## Protocolo de Transferencia de Archivos Trivial (TFTP)

El **Protocolo de Transferencia de Archivos Trivial (TFTP)** es más sencillo que FTP. TFTP no proporciona autenticación de usuarios ni otras funciones avanzadas. A diferencia de FTP, que utiliza **TCP**, TFTP utiliza **UDP**, lo que lo convierte en un protocolo menos fiable y depende de la capa de aplicación para la recuperación de errores.

### **Características de TFTP**

* **No requiere autenticación**.
* No admite inicios de sesión protegidos por contraseña.
* Se limita a archivos y directorios que están configurados para ser accesibles globalmente.
* Es adecuado solo para redes **locales y protegidas** debido a la falta de seguridad.

### **Comandos en TFTP**

| Comando | Descripción                                                                   |
| ------- | ----------------------------------------------------------------------------- |
| connect | Establece el host remoto y, opcionalmente, el puerto para las transferencias. |
| get     | Transfiere un archivo o conjunto de archivos del host remoto al host local.   |
| put     | Transfiere un archivo o conjunto de archivos del host local al remoto.        |
| quit    | Sale de TFTP.                                                                 |
| status  | Muestra el estado actual de TFTP.                                             |
| verbose | Activa o desactiva el modo detallado.                                         |

A diferencia del cliente FTP, TFTP **no tiene funcionalidad de listado de directorios**.

### Configuración por Defecto de vsFTPd

Uno de los servidores FTP más utilizados en distribuciones de Linux es **vsFTPd**. La configuración predeterminada se encuentra en `/etc/vsftpd.conf`.

#### **Instalación de vsFTPd**

```bash
sherlock28@htb[/htb]$ sudo apt install vsftpd
```

### **Archivo de Configuración de vsFTPd**

A continuación, se muestran algunas de las configuraciones comunes de **vsFTPd**:

| Configuración                                                  | Descripción                                        |
| -------------------------------------------------------------- | -------------------------------------------------- |
| listen=NO                                                      | ¿Ejecutar desde inetd o como daemon independiente? |
| listen\_ipv6=YES                                               | ¿Escuchar en IPv6?                                 |
| anonymous\_enable=NO                                           | ¿Permitir acceso anónimo?                          |
| local\_enable=YES                                              | ¿Permitir el inicio de sesión de usuarios locales? |
| dirmessage\_enable=YES                                         | ¿Mostrar mensajes de directorio?                   |
| use\_localtime=YES                                             | ¿Usar la hora local?                               |
| xferlog\_enable=YES                                            | ¿Activar el registro de transferencias?            |
| connect\_from\_port\_20=YES                                    | ¿Conectar desde el puerto 20?                      |
| secure\_chroot\_dir=/var/run/vsftpd/empty                      | Directorio vacío para conexiones chroot.           |
| pam\_service\_name=vsftpd                                      | Nombre del servicio PAM utilizado.                 |
| rsa\_cert\_file=/etc/ssl/certs/ssl-cert-snakeoil.pem           | Ubicación del certificado RSA para SSL.            |
| rsa\_private\_key\_file=/etc/ssl/private/ssl-cert-snakeoil.key | Clave privada RSA.                                 |
| ssl\_enable=NO                                                 | ¿Habilitar SSL?                                    |

#### **Archivo `/etc/ftpusers`**

Este archivo contiene una lista de usuarios a los que se les niega el acceso al servicio FTP, incluso si existen en el sistema Linux:

```bash
sherlock28@htb[/htb]$ cat /etc/ftpusers
guest
john
kevin
```

## Configuraciones Peligrosas

Existen muchas configuraciones relacionadas con la seguridad que podemos hacer en cada servidor FTP. Estas pueden tener diversos propósitos, como probar conexiones a través de cortafuegos, probar rutas y mecanismos de autenticación. Uno de estos mecanismos de autenticación es el **usuario anónimo**. Esto se utiliza a menudo para permitir que todos en la red interna compartan archivos y datos sin acceder a las computadoras de los demás. Con **vsFTPd**, las configuraciones opcionales que se pueden agregar al archivo de configuración para el inicio de sesión anónimo se ven de la siguiente manera:

| Configuración                  | Descripción                                                                                      |
| ------------------------------ | ------------------------------------------------------------------------------------------------ |
| `anonymous_enable=YES`         | ¿Permitir inicio de sesión anónimo?                                                              |
| `anon_upload_enable=YES`       | ¿Permitir que los anónimos suban archivos?                                                       |
| `anon_mkdir_write_enable=YES`  | ¿Permitir que los anónimos creen nuevos directorios?                                             |
| `no_anon_password=YES`         | ¿No pedir contraseña al anónimo?                                                                 |
| `anon_root=/home/username/ftp` | Directorio para el usuario anónimo.                                                              |
| `write_enable=YES`             | Permitir el uso de comandos FTP: `STOR`, `DELE`, `RNFR`, `RNTO`, `MKD`, `RMD`, `APPE`, y `SITE`. |

Con el cliente FTP estándar (`ftp`), podemos acceder al servidor FTP en consecuencia y iniciar sesión como usuario anónimo si se han utilizado las configuraciones anteriores. El uso de la cuenta anónima puede darse en entornos e infraestructuras internas donde todos los participantes son conocidos. El acceso a este tipo de servicio puede configurarse de manera temporal o con la finalidad de acelerar el intercambio de archivos.

Tan pronto como nos conectamos al servidor **vsFTPd**, se muestra el **código de respuesta 220** con el banner del servidor FTP. A menudo, este banner contiene la descripción del servicio e incluso su versión, lo que nos puede ayudar a obtener más información sobre el servidor y potenciales vulnerabilidades.

### **Conexión Anónima**

```bash
sherlock28@htb[/htb]$ ftp 10.129.14.136

Connected to 10.129.14.136.
220 "Welcome to the HTB Academy vsFTP service."
Name (10.129.14.136:cry0l1t3): anonymous

230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

Después de conectarnos como anónimos, podemos listar los archivos disponibles:

```bash
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.
```

### **Comando `status`**

Podemos usar el comando **`status`** para obtener una vista general de las configuraciones del servidor:

```bash
ftp> status

Connected to 10.129.14.136.
No proxy connection.
Connecting using address family: any.
Mode: stream; Type: binary; Form: non-print; Structure: file
Verbose: on; Bell: off; Prompting: on; Globbing: on
Store unique: off; Receive unique: off
Case: off; CR stripping: on
Quote control characters: on
Ntrans: off
Nmap: off
Hash mark printing: off; Use of PORT cmds: on
Tick counter printing: off
```

El uso de comandos como **`debug`** y **`trace`** puede proporcionar información detallada adicional que nos permita entender mejor la estructura y el comportamiento del servidor FTP.

### **Configuraciones Adicionales**

| Configuración             | Descripción                                                             |
| ------------------------- | ----------------------------------------------------------------------- |
| `dirmessage_enable=YES`   | ¿Mostrar un mensaje al entrar a un nuevo directorio?                    |
| `chown_uploads=YES`       | ¿Cambiar la propiedad de los archivos subidos anónimamente?             |
| `chown_username=username` | Usuario al que se le asigna la propiedad de los archivos subidos.       |
| `local_enable=YES`        | ¿Permitir que los usuarios locales inicien sesión?                      |
| `chroot_local_user=YES`   | ¿Limitar a los usuarios locales a su directorio home?                   |
| `chroot_list_enable=YES`  | ¿Usar una lista de usuarios locales que serán limitados a su home?      |
| `hide_ids=YES`            | ¿Ocultar los nombres de usuarios y grupos en las listas de directorios? |
| `ls_recurse_enable=YES`   | ¿Permitir listados recursivos de directorios?                           |

Cuando el **`hide_ids=YES`** está activo, los UIDs y GIDs se muestran como "ftp", dificultando la identificación de los archivos.

```bash
ftp> ls
150 Here comes the directory listing.
-rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees
-rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt
226 Directory send OK.
```

El ajuste de **`ls_recurse_enable=YES`** permite listar de manera recursiva todas las carpetas y archivos visibles en el servidor FTP.

### Ocultación de IDs - SÍ

```bash
ftp> ls
```

```plaintext
---> TYPE A
200 Switching to ASCII mode.
ftp: setsockopt (ignored): Permission denied
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp     ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt
226 Directory send OK.
```

Esta configuración es una medida de seguridad para evitar que se revelen los nombres de usuario locales. Con los nombres de usuario, en teoría, se podrían lanzar ataques de fuerza bruta a servicios como FTP y SSH. Sin embargo, en la práctica, soluciones como **fail2ban** son un estándar que registra la dirección IP y bloquea el acceso tras un número determinado de intentos fallidos.

Otra configuración útil es `ls_recurse_enable=YES`, comúnmente usada en servidores vsFTPd para tener una mejor vista de la estructura del directorio FTP, ya que permite ver todo el contenido visible de una sola vez.

### Listado Recursivo

```bash
ftp> ls -R
```

```plaintext
---> PORT 10,10,14,4,222,149
200 PORT command successful. Consider using PASV.
---> LIST -R
150 Here comes the directory listing.
.:
-rw-rw-r--    1 ftp      ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp      ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp      ftp            0 Sep 15 14:57 testupload.txt

./Clients:
drwx------    2 ftp      ftp          4096 Sep 16 18:04 HackTheBox
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:00 Inlanefreight

./Clients/HackTheBox:
-rw-r--r--    1 ftp      ftp         34872 Sep 16 18:04 appointments.xlsx
-rw-r--r--    1 ftp      ftp        498123 Sep 16 18:04 contract.docx
-rw-r--r--    1 ftp      ftp        478237 Sep 16 18:04 contract.pdf
-rw-r--r--    1 ftp      ftp           348 Sep 16 18:04 meetings.txt

./Clients/Inlanefreight:
-rw-r--r--    1 ftp      ftp         14211 Sep 16 18:00 appointments.xlsx
-rw-r--r--    1 ftp      ftp         37882 Sep 16 17:58 contract.docx
-rw-r--r--    1 ftp      ftp            89 Sep 16 17:58 meetings.txt
-rw-r--r--    1 ftp      ftp        483293 Sep 16 17:59 proposal.pptx

./Documents:
-rw-r--r--    1 ftp      ftp         23211 Sep 16 18:05 appointments-template.xlsx
-rw-r--r--    1 ftp      ftp         32521 Sep 16 18:05 contract-template.docx
-rw-r--r--    1 ftp      ftp        453312 Sep 16 18:05 contract-template.pdf

./Employees:
226 Directory send OK.
```

La descarga de archivos desde un servidor FTP es una de las principales funcionalidades, junto con la subida de archivos creados por nosotros. Esto nos permite, por ejemplo, aprovechar vulnerabilidades LFI (Local File Inclusion) para hacer que el servidor ejecute comandos del sistema.

### Descargar un Archivo

**FTP**

```bash
ftp> ls
```

```plaintext
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxrwxrwx    1 ftp      ftp             0 Sep 16 17:24 Calendar.pptx
drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees
-rwxrwxrwx    1 ftp      ftp            41 Sep 18 15:58 Important Notes.txt
226 Directory send OK.
```

```bash
ftp> get Important\ Notes.txt
```

```plaintext
local: Important Notes.txt remote: Important Notes.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for Important Notes.txt (41 bytes).
226 Transfer complete.
41 bytes received in 0.00 secs (606.6525 kB/s)
```

```bash
ftp> exit
```

```bash
sherlock28@htb[/htb]$ ls | grep Notes.txt
'Important Notes.txt'
```

También podemos descargar todos los archivos y carpetas a las que tenemos acceso de una sola vez, lo cual es útil si el servidor FTP tiene una estructura grande de archivos. Sin embargo, esto podría activar alarmas porque, en general, nadie en la empresa descargaría todo el contenido de una sola vez.

### Descargar Todos los Archivos Disponibles

```bash
sherlock28@htb[/htb]$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

```plaintext
--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/                                         
           => ‘10.129.14.136/.listing’                                                                     
Connecting to 10.129.14.136:21... connected.                                                               
Logging in as anonymous ... Logged in!
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD not needed.
==> PORT ... done.    ==> LIST ... done.                                                                 
12.12.1.136/.listing           [ <=>                                  ]     466  --.-KB/s    in 0s       
                                                                                                         
2021-09-19 14:45:58 (65,8 MB/s) - ‘10.129.14.136/.listing’ saved [466]                                     
--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/Calendar.pptx   
           => ‘10.129.14.136/Calendar.pptx’                                       
==> CWD not required.                                                           
==> SIZE Calendar.pptx ... done.                                                                                                                            
==> PORT ... done.    ==> RETR Calendar.pptx ... done.
```

Una vez que se han descargado todos los archivos, `wget` creará un directorio con el nombre de la dirección IP del objetivo. Allí se almacenarán todos los archivos descargados, que podremos inspeccionar localmente.

#### **Estructura de Archivos Descargados**

```bash
sherlock28@htb[/htb]$ tree .
```

```plaintext
plaintextCopiar código.
└── 10.129.14.136
    ├── Calendar.pptx
    ├── Clients
    │   └── Inlanefreight
    │       ├── appointments.xlsx
    │       ├── contract.docx
    │       ├── meetings.txt
    │       └── proposal.pptx
    ├── Documents
    │   ├── appointments-template.xlsx
    │   ├── contract-template.docx
    │   └── contract-template.pdf
    ├── Employees
    └── Important Notes.txt
5 directories, 9 files
```

### Subir un Archivo

```bash
sherlock28@htb[/htb]$ touch testupload.txt
```

Con el comando `PUT`, podemos subir archivos desde la carpeta actual al servidor FTP.

```bash
ftp> put testupload.txt 

local: testupload.txt remote: testupload.txt
---> PORT 10,10,14,4,184,33
200 PORT command successful. Consider using PASV.
---> STOR testupload.txt
150 Ok to send data.
226 Transfer complete.
```

Verificamos el archivo subido:

```bash
ftp> ls

---> TYPE A
200 Switching to ASCII mode.
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 1002     133             0 Sep 15 14:57 testupload.txt
226 Directory send OK.
```

## Footprinting the Service

El footprinting con herramientas de escaneo de red es una técnica común para identificar servicios, incluso si no están accesibles en puertos estándar. Una de las herramientas más usadas es **Nmap**, que incluye el motor de scripting NSE para análisis avanzados.

Podemos actualizar la base de datos de scripts NSE con el siguiente comando:

```bash
sherlock28@htb[/htb]$ sudo nmap --script-updatedb

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:49 CEST
NSE: Updating rule database.
NSE: Script Database updated successfully.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.28 seconds
```

Para buscar los scripts relacionados con FTP en nuestro sistema, usamos:

```bash
sherlock28@htb[/htb]$ find / -type f -name ftp* 2>/dev/null | grep scripts

/usr/share/nmap/scripts/ftp-syst.nse
/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-bounce.nse
/usr/share/nmap/scripts/ftp-libopie.nse
/usr/share/nmap/scripts/ftp-anon.nse
/usr/share/nmap/scripts/ftp-brute.nse
```

Ya que el servidor FTP suele correr en el puerto TCP 21, realizamos un escaneo de Nmap:

### Nmap

```bash
sherlock28@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-16 18:12 CEST
Nmap scan report for 10.129.14.136
Host is up (0.00013s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rwxrwxrwx    1 ftp      ftp       8138592 Sep 16 17:24 Calendar.pptx [NSE: writeable]
| drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees [NSE: writeable]
| -rwxrwxrwx    1 ftp      ftp            41 Sep 16 17:24 Important Notes.txt [NSE: writeable]
|_-rwxrwxrwx    1 ftp      ftp             0 Sep 15 14:57 testupload.txt [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
```

Este análisis muestra si el acceso FTP anónimo está permitido, además de otras configuraciones del servidor.

Para ver los comandos enviados y las respuestas del servidor, podemos usar el siguiente comando con **script-trace**:

### Nmap con script-trace

```bash
sherlock28@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #2 [10.129.14.136:21] (timeout: 9000ms) EID 50
NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.
```

El historial del escaneo muestra múltiples conexiones paralelas al servicio.

### Interacción con el Servicio

Para interactuar con el servidor FTP, podemos usar `netcat` o `telnet`:

```bash
sherlock28@htb[/htb]$ nc -nv 10.129.14.136 21

sherlock28@htb[/htb]$ telnet 10.129.14.136 21
```

Si el servidor FTP usa TLS/SSL, podemos usar **openssl** para la conexión:

```bash
sherlock28@htb[/htb]$ openssl s_client -connect 10.129.14.136:21 -starttls ftp

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
```

El certificado SSL puede revelar información útil, como el nombre de la organización o un correo electrónico.
