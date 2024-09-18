# Linux Remote Management Protocols

En el mundo de las distribuciones de Linux, existen muchas formas de gestionar los servidores de manera remota. Por ejemplo, imaginemos que estamos en una ubicación y uno de nuestros empleados, que acaba de visitar a un cliente en otra ciudad, necesita nuestra ayuda debido a un error que no puede resolver. En la mayoría de los casos, la solución de problemas a través de una llamada telefónica sería ineficiente, por lo que es beneficioso saber cómo iniciar sesión en el sistema remoto para gestionarlo.

Estas aplicaciones y servicios se pueden encontrar en casi todos los servidores en la red pública. Ahorran tiempo, ya que no es necesario estar físicamente presente en el servidor, y el entorno de trabajo sigue siendo el mismo. Por estas razones, los protocolos y aplicaciones para la gestión remota de sistemas son un objetivo interesante. Si la configuración es incorrecta, nosotros, como testers de penetración, podríamos incluso obtener acceso rápidamente al sistema remoto. Por lo tanto, es fundamental familiarizarnos con los protocolos, servidores y aplicaciones más importantes para este propósito.

## SSH

SSH, o Secure Shell, permite que dos computadoras establezcan una conexión encriptada y directa dentro de una red posiblemente insegura, utilizando el puerto estándar TCP 22. Esta conexión es necesaria para evitar que terceros intercepten el flujo de datos y, por lo tanto, datos sensibles. El servidor SSH también puede configurarse para permitir conexiones solo desde clientes específicos. Una ventaja de SSH es que el protocolo funciona en todos los sistemas operativos comunes. Como es una aplicación originalmente de Unix, también se implementa de forma nativa en todas las distribuciones de Linux y macOS. SSH también puede utilizarse en Windows, siempre que instalemos un programa adecuado.

El conocido servidor OpenBSD SSH (OpenSSH) en las distribuciones de Linux es un fork de código abierto del servidor SSH original y comercial de SSH Communication Security. En consecuencia, hay dos protocolos que compiten entre sí: SSH-1 y SSH-2.

SSH-2, también conocido como SSH versión 2, es un protocolo más avanzado que SSH versión 1 en términos de encriptación, velocidad, estabilidad y seguridad. Por ejemplo, SSH-1 es vulnerable a ataques MITM (man-in-the-middle), mientras que SSH-2 no lo es.

Podemos imaginar que queremos gestionar un host remoto. Esto puede hacerse a través de la línea de comandos o de una interfaz gráfica (GUI). Además, también podemos usar el protocolo SSH para enviar comandos al sistema deseado, transferir archivos o realizar reenvío de puertos. Por lo tanto, necesitamos conectarnos utilizando el protocolo SSH y autenticarnos.

En total, OpenSSH tiene seis métodos de autenticación diferentes:

* Autenticación por contraseña
* Autenticación por clave pública
* Autenticación basada en el host
* Autenticación por teclado
* Autenticación de desafío-respuesta
* Autenticación GSSAPI

A continuación, vamos a analizar más detenidamente uno de los métodos de autenticación más utilizados. Además, podemos aprender más sobre los otros métodos de autenticación aquí entre otros.

### Autenticación por Clave Pública

En un primer paso, el servidor y el cliente SSH se autentican mutuamente. El servidor envía un certificado al cliente para verificar que es el servidor correcto. Solo cuando se establece el primer contacto existe el riesgo de que un tercero se interponga entre los dos participantes y, por lo tanto, intercepte la conexión. Dado que el certificado en sí también está encriptado, no puede ser imitado. Una vez que el cliente conoce el certificado correcto, nadie más puede pretender contactar a través del servidor correspondiente.

Sin embargo, después de la autenticación del servidor, el cliente también debe demostrar al servidor que tiene autorización de acceso. El servidor SSH ya está en posesión del valor hash encriptado de la contraseña establecida para el usuario deseado. Como resultado, los usuarios deben ingresar la contraseña cada vez que se conectan a otro servidor durante la misma sesión. Por esta razón, una opción alternativa para la autenticación del lado del cliente es el uso de un par de claves pública y privada.

La clave privada se crea individualmente para la computadora del usuario y se protege con una frase de paso (passphrase) que debe ser más larga que una contraseña típica. La clave privada se almacena exclusivamente en nuestra propia computadora y siempre permanece secreta. Si queremos establecer una conexión SSH, primero ingresamos la frase de paso, lo que nos permite acceder a la clave privada.

Las claves públicas también se almacenan en el servidor. El servidor crea un problema criptográfico con la clave pública del cliente y lo envía al cliente. A su vez, el cliente desencripta el problema con su propia clave privada, envía la solución y, de esta manera, informa al servidor que puede establecer una conexión legítima. Durante una sesión, los usuarios solo necesitan ingresar la frase de paso una vez para conectarse a cualquier cantidad de servidores. Al finalizar la sesión, los usuarios cierran su sesión en sus máquinas locales, asegurando que ningún tercero que obtenga acceso físico a la máquina local pueda conectarse al servidor.

## Configuración Predeterminada

El archivo `sshd_config`, responsable del servidor OpenSSH, tiene solo algunas configuraciones predeterminadas. Sin embargo, la configuración predeterminada incluye el reenvío de X11, que contenía una vulnerabilidad de inyección de comandos en la versión 7.2p1 de OpenSSH en 2016. No obstante, no necesitamos una interfaz gráfica (GUI) para gestionar nuestros servidores.

```bash
sherlock28@htb[/htb]$ cat /etc/ssh/sshd_config | grep -v "#" | sed -r '/^\s*$/d'

Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
```

La mayoría de las configuraciones en este archivo están comentadas y requieren configuración manual.

## Configuraciones Peligrosas

A pesar de que el protocolo SSH es uno de los más seguros disponibles hoy en día, algunas configuraciones incorrectas pueden hacer que el servidor SSH sea vulnerable a ataques fáciles de ejecutar. Veamos las siguientes configuraciones:

| Configuración                | Descripción                                      |
| ---------------------------- | ------------------------------------------------ |
| `PasswordAuthentication yes` | Permite la autenticación basada en contraseña.   |
| `PermitEmptyPasswords yes`   | Permite el uso de contraseñas vacías.            |
| `PermitRootLogin yes`        | Permite iniciar sesión como el usuario root.     |
| `Protocol 1`                 | Utiliza una versión obsoleta de encriptación.    |
| `X11Forwarding yes`          | Permite el reenvío de X11 para aplicaciones GUI. |
| `AllowTcpForwarding yes`     | Permite el reenvío de puertos TCP.               |
| `PermitTunnel`               | Permite el uso de túneles.                       |
| `DebianBanner yes`           | Muestra un banner específico al iniciar sesión.  |

Permitir la autenticación por contraseña permite que se pueda realizar un ataque de fuerza bruta a un nombre de usuario conocido para adivinar posibles contraseñas. Se pueden utilizar muchos métodos diferentes para adivinar las contraseñas de los usuarios. Para este propósito, generalmente se utilizan patrones específicos para mutar las contraseñas más comunes y, alarmantemente, suelen ser correctos. Esto se debe a que los humanos tendemos a crear contraseñas fáciles de recordar, lo que lleva a que, por ejemplo, solo se agreguen números o caracteres al final de la contraseña. Creyendo que la contraseña es segura, estos patrones se utilizan para adivinar precisamente estos "ajustes". Sin embargo, existen guías y recomendaciones de endurecimiento que se pueden utilizar para mejorar la seguridad de nuestros servidores SSH.

## Huella del Servicio (Footprinting)

Uno de los **herramientas** que podemos utilizar para identificar el servidor SSH es **ssh-audit**. Este script verifica la configuración del cliente y del servidor, mostrando información general y los algoritmos de cifrado utilizados por ambos. Esta información podría ser explotada más adelante atacando al servidor o cliente a nivel criptográfico.

### **SSH-Audit**

```bash
sherlock28@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
sherlock28@htb[/htb]$ ./ssh-audit.py 10.129.14.132
```

**Salida del Comando**

```bash
# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
(kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
(kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
(kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
(kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
(kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
(kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73

# host-key algorithms
(key) rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
(key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
(key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
```

Lo primero que podemos observar en las primeras líneas es el **banner**, que revela la versión del servidor OpenSSH. Las versiones anteriores tenían algunas vulnerabilidades, como la CVE-2020-14145, que permitía ataques Man-In-The-Middle (MITM) y atacar el intento de conexión inicial. El detalle de la configuración de la conexión también puede proporcionar información importante, como los métodos de autenticación disponibles en el servidor.

### Cambiar Método de Autenticación

Podemos especificar el método de autenticación con la opción `PreferredAuthentications` del cliente SSH para ataques de fuerza bruta.

```bash
sherlock28@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

**Salida del Comando**

```bash
OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password
cry0l1t3@10.129.14.132's password:
```

Incluso con este servicio seguro, se recomienda configurar nuestro propio servidor OpenSSH en una máquina virtual (VM), experimentar con él y familiarizarnos con las diferentes configuraciones y opciones.

Podemos encontrarnos con diferentes **banners** de servidores SSH durante nuestras pruebas de penetración. Estos banners generalmente comienzan con la versión del protocolo y la versión del servidor. Por ejemplo, con `SSH-1.99-OpenSSH_3.9p1`, sabemos que podemos usar tanto la versión de protocolo SSH-1 como SSH-2, y estamos tratando con el servidor OpenSSH versión 3.9p1. Por otro lado, un banner como `SSH-2.0-OpenSSH_8.2p1` indica que solo se acepta la versión de protocolo SSH-2.

## Rsync

Rsync es una herramienta rápida y eficiente para copiar archivos localmente y de forma remota. Se puede utilizar para copiar archivos en una máquina local o hacia/desde hosts remotos. Es altamente versátil y conocida por su algoritmo de transferencia delta. Este algoritmo reduce la cantidad de datos transmitidos por la red cuando ya existe una versión del archivo en el host de destino. Lo hace enviando solo las diferencias entre los archivos fuente y la versión anterior de los archivos que residen en el servidor de destino. A menudo se utiliza para copias de seguridad y espejos. Rsync identifica los archivos que deben transferirse observando aquellos que han cambiado de tamaño o tiempo de modificación. Por defecto, utiliza el puerto **873** y puede configurarse para usar **SSH** para transferencias seguras de archivos, aprovechando una conexión SSH establecida.

Esta guía cubre algunas formas en las que Rsync puede ser abusado, principalmente listando el contenido de una carpeta compartida en un servidor objetivo y recuperando archivos. A veces esto se puede hacer sin autenticación, y otras veces necesitaremos credenciales. Si encontramos credenciales durante una prueba de penetración y detectamos Rsync en un host interno (o externo), siempre vale la pena verificar si se reutilizan contraseñas, ya que podríamos obtener archivos sensibles que podrían ser utilizados para ganar acceso remoto al objetivo.

### Escaneo de Rsync

Podemos verificar que Rsync está en uso utilizando el **protocolo 31**.

```bash
sherlock28@htb[/htb]$ sudo nmap -sV -p 873 127.0.0.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-19 09:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0058s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.13 seconds
```

### Investigación de Shares Accesibles

Luego, podemos investigar un poco más el servicio para ver a qué podemos acceder.

```bash
sherlock28@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

### Enumeración de un Share Abierto

Aquí podemos ver un share llamado `dev`, y podemos enumerarlo más a fondo.

```bash
sherlock28@htb[/htb]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```

Del resultado anterior, podemos ver algunos archivos interesantes que podrían valer la pena descargar para investigarlos más a fondo. También podemos ver que un directorio que probablemente contiene claves SSH es accesible. Desde aquí, podríamos sincronizar todos los archivos a nuestro host de ataque con el siguiente comando:

```bash
rsync -av rsync://127.0.0.1/dev
```

Si Rsync está configurado para usar SSH para transferir archivos, podríamos modificar nuestros comandos para incluir la opción `-e ssh`, o `-e "ssh -p2222"` si se está usando un puerto no estándar para SSH.

## Servicios R

Los **R-Services** son un conjunto de servicios que permiten el acceso remoto o la ejecución de comandos entre hosts Unix a través de TCP/IP. Inicialmente desarrollados por el **Computer Systems Research Group (CSRG)** de la Universidad de California, Berkeley, los servicios R fueron el estándar de facto para el acceso remoto entre sistemas operativos Unix hasta que fueron reemplazados por los protocolos y comandos de **Secure Shell (SSH)** debido a las fallas de seguridad inherentes en ellos. Al igual que telnet, los R-Services transmiten información del cliente al servidor (y viceversa) en formato no cifrado, lo que permite a los atacantes interceptar el tráfico de red (contraseñas, información de inicio de sesión, etc.) a través de ataques de intermediario (MITM).

Los R-Services se ejecutan en los puertos **512**, **513** y **514**, y solo son accesibles mediante un conjunto de programas conocidos como **r-commands**. Aunque son menos comunes en la actualidad, a veces se encuentran durante pruebas de penetración internas, por lo que es importante entender cómo abordarlos.

El conjunto de **R-commands** incluye los siguientes programas:

* **rcp (remote copy)**: Copia archivos entre sistemas.
* **rexec (remote execution)**: Ejecuta comandos en un sistema remoto.
* **rlogin (remote login)**: Permite iniciar sesión en un host remoto.
* **rsh (remote shell)**: Abre una shell en una máquina remota sin procedimiento de inicio de sesión.
* **rstat**
* **ruptime**
* **rwho (remote who)**

A continuación, una tabla con los comandos más frecuentemente abusados, el daemon del servicio que utilizan, el puerto y una breve descripción.

| Comando    | Servicio Daemon | Puerto | Protocolo de Transporte | Descripción                                                                                                                                                                               |
| ---------- | --------------- | ------ | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rcp**    | rshd            | 514    | TCP                     | Copia archivos o directorios bidireccionalmente entre sistemas locales y remotos, sin advertencias al sobrescribir archivos.                                                              |
| **rsh**    | rshd            | 514    | TCP                     | Abre una shell en una máquina remota sin procedimiento de inicio de sesión. Usa los archivos `/etc/hosts.equiv` y `.rhosts` para validación.                                              |
| **rexec**  | rexecd          | 512    | TCP                     | Ejecuta comandos shell en un sistema remoto. Requiere autenticación con usuario y contraseña, pero puede ser reemplazada por las entradas de confianza en `/etc/hosts.equiv` y `.rhosts`. |
| **rlogin** | rlogind         | 513    | TCP                     | Permite a un usuario iniciar sesión en un host remoto. Similar a telnet pero solo para hosts tipo Unix.                                                                                   |

***

### Archivo `/etc/hosts.equiv`

El archivo **`/etc/hosts.equiv`** contiene una lista de hosts confiables que permiten el acceso sin autenticación adicional.

```bash
sherlock28@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```

### Escaneo de R-Services

Podemos realizar un escaneo rápido con **Nmap** para determinar si los puertos necesarios están abiertos.

```bash
sherlock28@htb[/htb]$ sudo nmap -sV -p 512,513,514 10.0.17.2

Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-02 15:02 EST
Nmap scan report for 10.0.17.2
Host is up (0.11s latency).

PORT    STATE SERVICE    VERSION
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 145.54 seconds
```

### Control de Acceso y Relaciones de Confianza

El principal problema de los R-services es la falta de control de acceso. Estos protocolos dependen de la información confiable enviada desde el cliente remoto al host al que intentan autenticarse. Además de usar **PAM** para la autenticación, pueden omitir esta autenticación mediante los archivos **`/etc/hosts.equiv`** y **`.rhosts`**. Estos archivos contienen una lista de hosts o usuarios confiables que permiten el acceso sin necesidad de contraseñas.

### **Ejemplo de archivo `.rhosts`**

```bash
sherlock28@htb[/htb]$ cat .rhosts

htb-student     10.0.17.5
+               10.0.17.10
+               +
```

En este ejemplo, el modificador `+` permite a cualquier usuario externo acceder a los r-commands desde la cuenta del usuario `htb-student` a través del host con la IP `10.0.17.10`.

Las configuraciones incorrectas en estos archivos pueden permitir a un atacante autenticarse como otro usuario sin credenciales, con la posibilidad de obtener ejecución de código. Ahora que comprendemos cómo pueden abusarse estas configuraciones, podríamos intentar iniciar sesión en un host objetivo usando **rlogin**.

### Iniciando Sesión Usando Rlogin

Podemos aprovechar las configuraciones incorrectas en el archivo **`.rhosts`** para iniciar sesión en un host remoto sin necesidad de autenticación. A continuación, se muestra un ejemplo de cómo iniciar sesión usando **rlogin**.

```bash
sherlock28@htb[/htb]$ rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$
```

Hemos iniciado sesión correctamente bajo la cuenta **htb-student** en el host remoto gracias a la mala configuración del archivo **`.rhosts`**. Una vez dentro, podemos aprovechar el comando **rwho** para listar todas las sesiones interactivas en la red local enviando solicitudes al puerto UDP 513.

### Listando Usuarios Autenticados Usando Rwho

```bash
sherlock28@htb[/htb]$ rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25       
```

De esta información, podemos ver que el usuario **htb-student** está actualmente autenticado en el host **workstn01**, mientras que el usuario **root** está autenticado en el host **web01**. Esta información puede ser útil para identificar nombres de usuarios potenciales a los que dirigir ataques adicionales en la red. Sin embargo, dado que el daemon **rwho** transmite periódicamente información sobre los usuarios conectados, también puede ser útil monitorear el tráfico de red.

### Listando Usuarios Autenticados Usando Rusers

El comando **rusers** proporciona información más detallada sobre los usuarios conectados en la red, como el nombre de usuario, el host al que están conectados, la terminal (TTY), la fecha y hora de inicio de sesión, y más.

```bash
sherlock28@htb[/htb]$ rusers -al 10.0.17.5

htb-student     10.0.17.5:console          Dec 2 19:57     2:25
```

## Reflexiones Finales

Los **R-Services** son cada vez menos utilizados debido a sus fallas de seguridad inherentes y la disponibilidad de protocolos más seguros como **SSH**. Sin embargo, es importante estar familiarizado con ellos, ya que en algunas pruebas de penetración o entornos antiguos pueden seguir presentes.

Los servicios de gestión remota pueden proporcionarnos una gran cantidad de datos y, a menudo, ser explotados para obtener acceso no autorizado mediante credenciales débiles o reutilización de contraseñas. Siempre debemos investigar estos servicios a fondo para recolectar toda la información posible, ya que podría ser clave en futuras etapas del ataque.
