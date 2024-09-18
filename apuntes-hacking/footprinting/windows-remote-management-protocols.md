# Windows Remote Management Protocols

Windows servers pueden ser administrados localmente utilizando las tareas de administración de Server Manager en servidores remotos. La administración remota está habilitada de manera predeterminada a partir de Windows Server 2016. La administración remota es un componente de las características de administración de hardware de Windows que gestiona el hardware del servidor local y remotamente. Estas características incluyen un servicio que implementa el protocolo WS-Management, diagnósticos de hardware y control a través de controladores de administración de placas base, y una API COM y objetos de script que nos permiten escribir aplicaciones que se comuniquen remotamente a través del protocolo WS-Management.

Los principales componentes utilizados para la administración remota de Windows y servidores Windows son los siguientes:

* **Protocolo de Escritorio Remoto (RDP)**
* **Windows Remote Management (WinRM)**
* **Windows Management Instrumentation (WMI)**

## RDP

El Protocolo de Escritorio Remoto (RDP) es un protocolo desarrollado por Microsoft para el acceso remoto a una computadora que ejecuta el sistema operativo Windows. Este protocolo permite que los comandos de visualización y control se transmitan mediante la GUI encriptada a través de redes IP. RDP funciona en la capa de aplicación en el modelo de referencia TCP/IP, utilizando típicamente el puerto TCP 3389 como protocolo de transporte. Sin embargo, el protocolo sin conexión UDP también puede usar el puerto 3389 para la administración remota.

Para que se establezca una sesión RDP, tanto el firewall de la red como el firewall en el servidor deben permitir conexiones desde el exterior. Si se utiliza la Traducción de Direcciones de Red (NAT) en la ruta entre el cliente y el servidor, como suele ser el caso con las conexiones a Internet, la computadora remota necesita la dirección IP pública para acceder al servidor. Además, debe configurarse el reenvío de puertos en el enrutador NAT en dirección al servidor.

RDP ha manejado la Seguridad de Capa de Transporte (TLS/SSL) desde Windows Vista, lo que significa que todos los datos, y especialmente el proceso de inicio de sesión, están protegidos en la red mediante un buen cifrado. Sin embargo, muchos sistemas Windows no insisten en esto y aún aceptan cifrado inadecuado a través de la Seguridad de RDP. No obstante, con esto, un atacante aún está lejos de ser bloqueado porque los certificados que proporcionan la identidad son meramente autofirmados por defecto. Esto significa que el cliente no puede distinguir un certificado genuino de uno falsificado y genera una advertencia de certificado para el usuario.

El servicio de Escritorio Remoto está instalado de forma predeterminada en servidores Windows y no requiere aplicaciones externas adicionales. Este servicio puede activarse utilizando el Server Manager y viene con la configuración predeterminada para permitir conexiones al servicio solo a hosts con Autenticación a Nivel de Red (NLA).

## Footprinting the Service

Escanear el servicio de RDP puede proporcionarnos mucha información sobre el host. Por ejemplo, podemos determinar si NLA (Network Level Authentication) está habilitado en el servidor, la versión del producto y el nombre del host.

### **Nmap**

```bash
sherlock28@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

**Resultado del escaneo:**

```less
Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 15:45 CET
Nmap scan report for 10.129.201.248
Host is up (0.036s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|_    RDSTLS: SUCCESS
| rdp-ntlm-info: 
|   Target_Name: ILF-SQL-01
|   NetBIOS_Domain_Name: ILF-SQL-01
|   NetBIOS_Computer_Name: ILF-SQL-01
|   DNS_Domain_Name: ILF-SQL-01
|   DNS_Computer_Name: ILF-SQL-01
|   Product_Version: 10.0.17763
|_  System_Time: 2021-11-06T13:46:00+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Nmap done: 1 IP address (1 host up) scanned in 8.26 seconds
```

Adicionalmente, podemos usar la opción `--packet-trace` para rastrear los paquetes individuales e inspeccionar su contenido manualmente. Observamos que las cookies de RDP (mstshash=nmap), utilizadas por Nmap para interactuar con el servidor RDP, pueden ser identificadas por los cazadores de amenazas y varios servicios de seguridad, como Endpoint Detection and Response (EDR), lo que podría bloquear nuestra entrada como pentesters en redes endurecidas.

```bash
sherlock28@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n
```

**Resultado del escaneo con trazado de paquetes:**

```yaml
Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 16:23 CET
SENT (0.2506s) ICMP [10.10.14.20 > 10.129.201.248 Echo request (type=8/code=0) id=8338 seq=0] IP [ttl=53 id=5122 iplen=28 ]
SENT (0.2507s) TCP 10.10.14.20:55516 > 10.129.201.248:443 S ttl=42 id=24195 iplen=44  seq=1926233369 win=1024 <mss 1460>
...SNIP...
NSOCK INFO [6.6820s] nsock_read(): Read request from IOD #2 [10.129.201.248:3389] (timeout: 5000ms) EID 74
NSOCK INFO [6.7180s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 74 [10.129.201.248:3389] (211 bytes)
NSE: TCP 10.10.14.20:36630 < 10.129.201.248:3389 | 
00000000: 30 81 d0 a0 03 02 01 06 a1 81 c8 30 81 c5 30 81 0          0  0
00000010: c2 a0 81 bf 04 81 bc 4e 54 4c 4d 53 53 50 00 02        NTLMSSP
00000020: 00 00 00 14 00 14 00 38 00 00 00 35 82 8a e2 b9        8   5
00000030: 73 b0 b3 91 9f 1b 0d 00 00 00 00 00 00 00 00 70 s              p
00000040: 00 70 00 4c 00 00 00 0a 00 63 45 00 00 00 0f 49  p L     cE    I
00000050: 00 4c 00 46 00 2d 00 53 00 51 00 4c 00 2d 00 30  L F - S Q L - 0
00000060: 00 31 00 02 00 14 00 49 00 4c 00 46 00 2d 00 53  1     I L F - S
00000070: 00 51 00 4c 00 2d 00 30 00 31 00 01 00 14 00 49  Q L - 0 1     I
00000080: 00 4c 00 46 00 2d 00 53 00 51 00 4c 00 2d 00 30  L F - S Q L - 0
00000090: 00 31 00 04 00 14 00 49 00 4c 00 46 00 2d 00 53  1     I L F - S
000000a0: 00 51 00 4c 00 2d 00 30 00 31 00 03 00 14 00 49  Q L - 0 1     I
000000b0: 00 4c 00 46 00 2d 00 53 00 51 00 4c 00 2d 00 30  L F - S Q L - 0
000000c0: 00 31 00 07 00 08 00 1d b3 e8 f2 19 d3 d7 01 00  1
000000d0: 00 00 00
```

Un script en Perl llamado `rdp-sec-check.pl` fue desarrollado por **Cisco CX Security Labs**, que puede identificar sin autenticación la configuración de seguridad de los servidores RDP basándose en los handshakes.

### Verificación de Seguridad de RDP - Instalación

```bash
sherlock28@htb[/htb]$ sudo cpan
```

Al ejecutar **CPAN** para instalar módulos Perl, primero te pedirá configurar algunas opciones. La mayoría se pueden configurar automáticamente.

```vbnet
Loading internal logger. Log::Log4perl recommended for better logging

CPAN.pm requires configuration, but most of it can be done automatically.
If you answer 'no' below, you will enter an interactive dialog for each
configuration option instead.

Would you like to configure as much as possible automatically? [yes] yes

Autoconfiguration complete.

commit: wrote '/root/.cpan/CPAN/MyConfig.pm'

You can re-run configuration any time with 'o conf init' in the CPAN shell

cpan shell -- CPAN exploration and modules installation (v2.27)
Enter 'h' for help.
```

Luego, instalamos el módulo **Encoding::BER**:

```bash
cpan[1]> install Encoding::BER
```

Después de la instalación del módulo, clonamos el repositorio del script de verificación de seguridad de RDP:

### Verificación de Seguridad de RDP

```bash
sherlock28@htb[/htb]$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
sherlock28@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248
```

**Resultado del escaneo:**

```yaml
Starting rdp-sec-check v0.9-beta ( http://labs.portcullis.co.uk/application/rdp-sec-check/ ) at Sun Nov  7 16:50:32 2021

[+] Scanning 1 hosts

Target:    10.129.201.248
IP:        10.129.201.248
Port:      3389

[+] Checking supported protocols

[-] Checking if RDP Security (PROTOCOL_RDP) is supported...Not supported - HYBRID_REQUIRED_BY_SERVER
[-] Checking if TLS Security (PROTOCOL_SSL) is supported...Not supported - HYBRID_REQUIRED_BY_SERVER
[-] Checking if CredSSP Security (PROTOCOL_HYBRID) is supported [uses NLA]...Supported

[+] Checking RDP Security Layer

[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_NONE...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_40BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_128BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_56BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_FIPS...Not supported

[+] Summary of protocol support

[-] 10.129.201.248:3389 supports PROTOCOL_SSL   : FALSE
[-] 10.129.201.248:3389 supports PROTOCOL_HYBRID: TRUE
[-] 10.129.201.248:3389 supports PROTOCOL_RDP   : FALSE

[+] Summary of RDP encryption support

[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_NONE   : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_40BIT  : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_128BIT : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_56BIT  : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_FIPS   : FALSE

[+] Summary of security issues

rdp-sec-check v0.9-beta completed at Sun Nov  7 16:50:33 2021
```

**Conexión a servidores RDP en Linux**

Es posible autenticarse y conectarse a servidores RDP de varias maneras. En Linux, se pueden usar herramientas como **xfreerdp**, **rdesktop** o **Remmina** para interactuar con la GUI del servidor.

### Iniciar una Sesión RDP

Para iniciar una sesión de **RDP** en Linux utilizando **xfreerdp**, ejecuta el siguiente comando:

```bash
sherlock28@htb[/htb]$ xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```

**Proceso de conexión:**

```bash
[16:37:47:135] [95319:95320] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[16:37:47:135] [95319:95320] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
[16:37:47:135] [95319:95320] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd
[16:37:47:135] [95319:95320] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr
[16:37:47:447] [95319:95320] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized
[16:37:47:453] [95319:95320] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[16:37:47:453] [95319:95320] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state
[16:37:47:523] [95319:95320] [INFO][com.freerdp.crypto] - creating directory /home/cry0l1t3/.config/freerdp
[16:37:47:523] [95319:95320] [INFO][com.freerdp.crypto] - creating directory [/home/cry0l1t3/.config/freerdp/certs]
[16:37:47:523] [95319:95320] [INFO][com.freerdp.crypto] - created directory [/home/cry0l1t3/.config/freerdp/server]
```

**Advertencia de certificado:**

```bash
[16:37:47:599] [95319:95320] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[16:37:47:599] [95319:95320] [WARN][com.freerdp.crypto] - CN = ILF-SQL-01
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - @           WARNING: CERTIFICATE NAME MISMATCH!           @
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - The hostname used for this connection (10.129.201.248:3389) 
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - does not match the name given in the certificate:
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - Common Name (CN):
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] -      ILF-SQL-01
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - A valid certificate for the wrong name should NOT be trusted!
```

Detalles del certificado:

```ruby
Certificate details for 10.129.201.248:3389 (RDP-Server):
        Common Name: ILF-SQL-01
        Subject:     CN = ILF-SQL-01
        Issuer:      CN = ILF-SQL-01
        Thumbprint:  b7:5f:00:ca:91:00:0a:29:0c:b5:14:21:f3:b0:ca:9e:af:8c:62:d6:dc:f9:50:ec:ac:06:38:1f:c5:d6:a9:39
The above X.509 certificate could not be verified, possibly because you do not have
the CA certificate in your certificate store, or the certificate has expired.
Please look at the OpenSSL documentation on how to add a private CA to the store.

Do you trust the above certificate? (Y/T/N) y
```

Después de autenticar con éxito, se abrirá una nueva ventana con acceso al escritorio del servidor al cual te has conectado.

## WinRM&#x20;

**Windows Remote Management (WinRM)** es un protocolo de administración remota integrado en Windows que utiliza la línea de comandos. WinRM se basa en **SOAP (Simple Object Access Protocol)** para establecer conexiones con hosts remotos y sus aplicaciones. Este servicio debe habilitarse y configurarse explícitamente, comenzando con Windows 10.

WinRM utiliza los puertos **TCP 5985** y **5986** para la comunicación, con el puerto 5986 utilizando **HTTPS**. Anteriormente, los puertos **80** y **443** se usaban para esta tarea, pero debido a razones de seguridad, se adoptaron los nuevos puertos.

Otro componente útil en la administración remota es **Windows Remote Shell (WinRS)**, que permite ejecutar comandos en sistemas remotos. WinRS viene incluido en Windows 7 de forma predeterminada. A través de WinRM, se pueden ejecutar comandos en servidores remotos.

Servicios como sesiones remotas con PowerShell y la fusión de registros de eventos requieren que WinRM esté habilitado. En versiones de **Windows Server 2012** y posteriores, WinRM está activado por defecto, pero en versiones anteriores debe configurarse manualmente y habilitar las excepciones necesarias en el firewall.

## Footprinting del Servicio

Como ya sabemos, **WinRM** utiliza los puertos **TCP 5985 (HTTP)** y **5986 (HTTPS)** por defecto, los cuales podemos escanear usando **Nmap**. Sin embargo, a menudo veremos que solo se utiliza **HTTP (TCP 5985)** en lugar de **HTTPS (TCP 5986)**.

### **Escaneo de WinRM con Nmap**

Como WinRM utiliza los puertos **TCP 5985 (HTTP)** y **5986 (HTTPS)**, se pueden escanear estos puertos utilizando **Nmap**. En muchos casos, solo se usa el puerto **HTTP (5985)** en lugar de HTTPS.

```bash
sherlock28@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 16:31 CET
Nmap scan report for 10.129.201.248
Host is up (0.030s latency).

PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

El escaneo muestra que el puerto **5985 (HTTP)** está abierto y es utilizado por **Microsoft HTTPAPI**.

### **Comprobación de Conexiones WinRM**

En entornos **Windows**, se puede usar el cmdlet **Test-WsMan** de PowerShell para verificar si un servidor remoto está disponible a través de WinRM. En **Linux**, se puede usar la herramienta **evil-winrm**, diseñada para interactuar con WinRM.

### **Conexión usando Evil-WinRM**

```bash
sherlock28@htb[/htb]$ evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!

Evil-WinRM shell v3.3

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Cry0l1t3\Documents>
```

La conexión a través de **evil-winrm** se ha establecido correctamente y se ha obtenido acceso al sistema remoto, donde es posible ejecutar comandos.

## WMI

**Windows Management Instrumentation (WMI)** es la implementación de Microsoft y también una extensión del **Common Information Model (CIM)**, una funcionalidad central del estándar **Web-Based Enterprise Management (WBEM)** para la plataforma Windows. WMI permite acceso de lectura y escritura a casi todas las configuraciones de los sistemas Windows. Por lo tanto, es la interfaz más crítica en el entorno de Windows para la administración y mantenimiento remoto de computadoras con Windows, ya sean PC o servidores.

WMI se accede típicamente a través de **PowerShell**, **VBScript** o la **Windows Management Instrumentation Console (WMIC)**. No es un solo programa, sino que consiste en varios programas y diversas bases de datos, también conocidas como repositorios.

## Footprinting del Servicio

La inicialización de la comunicación con WMI siempre se realiza a través del puerto **TCP 135**, y luego de establecerse la conexión con éxito, la comunicación se traslada a un puerto aleatorio. Por ejemplo, el programa **wmiexec.py** del toolkit **Impacket** puede utilizarse para esto.

### WMIexec.py

```bash
Windows Remote Management Protocols
  sherlock28@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"

  Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

  [*] SMBv3.0 dialect used
  ILF-SQL-01
```

Es importante mencionar que el conocimiento adquirido al instalar estos servicios y experimentar con las configuraciones en nuestro propio servidor Windows no puede ser reemplazado por leer manuales. Por ello, recomendamos encarecidamente configurar tu propio **Windows Server**, experimentar con la configuración y escanear estos servicios repetidamente para observar las diferencias en los resultados.
