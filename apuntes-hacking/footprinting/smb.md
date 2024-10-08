# SMB

**SMB** es un protocolo cliente-servidor que regula el acceso a archivos y directorios, así como otros recursos de red como impresoras, routers o interfaces disponibles en la red. También permite el intercambio de información entre procesos de sistema distintos. SMB se hizo disponible al público en general, por ejemplo, como parte del sistema operativo de red OS/2 LAN Manager y LAN Server. Desde entonces, su aplicación principal ha sido en los sistemas operativos Windows, cuyos servicios de red soportan SMB de manera compatible hacia atrás, lo que significa que los dispositivos con versiones más recientes pueden comunicarse fácilmente con dispositivos que tengan un sistema operativo Microsoft más antiguo. Con el proyecto de software libre **Samba**, también hay una solución que permite el uso de SMB en distribuciones de Linux y Unix, facilitando la comunicación entre plataformas a través de SMB.

El protocolo SMB permite que el cliente se comunique con otros participantes en la misma red para acceder a archivos o servicios compartidos en la red. El otro sistema también debe haber implementado el protocolo de red y procesado la solicitud del cliente usando una aplicación de servidor SMB. Antes de eso, ambas partes deben establecer una conexión, por lo que primero intercambian mensajes correspondientes.

En redes IP, SMB utiliza el protocolo TCP para esto, el cual proporciona un apretón de manos de tres vías entre el cliente y el servidor antes de que se establezca finalmente una conexión. Las especificaciones del protocolo TCP también regulan el transporte de datos posterior. Veamos algunos ejemplos.

Un servidor SMB puede proporcionar partes arbitrarias de su sistema de archivos local como recursos compartidos. Por lo tanto, la jerarquía visible para un cliente es parcialmente independiente de la estructura en el servidor. Los derechos de acceso se definen mediante Listas de Control de Acceso (ACL). Estas pueden ser controladas de manera detallada basándose en atributos como ejecutar, leer y acceso total para usuarios individuales o grupos de usuarios. Las ACL se definen basándose en los recursos compartidos y, por lo tanto, no corresponden a los derechos asignados localmente en el servidor.

## Samba

Como se mencionó anteriormente, existe una variante alternativa del servidor SMB llamada **Samba**, desarrollada para sistemas operativos basados en Unix. Samba implementa el protocolo de red **Common Internet File System (CIFS)**. CIFS es un "dialecto" de SMB. En otras palabras, CIFS es una implementación muy específica del protocolo SMB, que a su vez fue creado por Microsoft. Esto permite que Samba se comunique con sistemas Windows más recientes. Por lo tanto, generalmente se refiere a SMB/CIFS. Sin embargo, CIFS es una extensión del protocolo SMB. Así que cuando enviamos comandos SMB a través de Samba a un servicio NetBIOS más antiguo, generalmente se conecta al servidor Samba a través de los puertos TCP 137, 138, 139, pero CIFS usa únicamente el puerto TCP 445. Existen varias versiones de SMB, incluidas versiones obsoletas que todavía se usan en infraestructuras específicas.

| **Versión SMB** | **Soportado en**                    | **Características**                                                                       |
| --------------- | ----------------------------------- | ----------------------------------------------------------------------------------------- |
| CIFS            | Windows NT 4.0                      | Comunicación a través de la interfaz NetBIOS                                              |
| SMB 1.0         | Windows 2000                        | Conexión directa a través de TCP                                                          |
| SMB 2.0         | Windows Vista, Windows Server 2008  | Mejoras en el rendimiento, firma de mensajes mejorada, función de almacenamiento en caché |
| SMB 2.1         | Windows 7, Windows Server 2008 R2   | Mecanismos de bloqueo                                                                     |
| SMB 3.0         | Windows 8, Windows Server 2012      | Conexiones multicanal, cifrado de extremo a extremo, acceso a almacenamiento remoto       |
| SMB 3.0.2       | Windows 8.1, Windows Server 2012 R2 | -                                                                                         |
| SMB 3.1.1       | Windows 10, Windows Server 2016     | Verificación de integridad, cifrado AES-128                                               |

Con la versión 3, el servidor Samba adquirió la capacidad de ser un miembro completo de un dominio de Active Directory. Con la versión 4, Samba incluso proporciona un controlador de dominio Active Directory. Contiene varios llamados _daemons_ para este propósito, que son programas en segundo plano de Unix. El _daemon_ del servidor SMB (smbd) de Samba proporciona las dos primeras funcionalidades, mientras que el _daemon_ del bloque de mensajes NetBIOS (nmbd) implementa las dos últimas funcionalidades. El servicio SMB controla estos dos programas en segundo plano.

Sabemos que Samba es adecuado tanto para sistemas Linux como Windows. En una red, cada host participa en el mismo grupo de trabajo. Un grupo de trabajo es un nombre de grupo que identifica una colección arbitraria de computadoras y sus recursos en una red SMB. Puede haber múltiples grupos de trabajo en la red en cualquier momento. IBM desarrolló una interfaz de programación de aplicaciones (API) para redes de computadoras llamada **Network Basic Input/Output System (NetBIOS)**. La API NetBIOS proporcionó un esquema para que una aplicación se conectara y compartiera datos con otras computadoras. En un entorno NetBIOS, cuando una máquina se conecta a la red, necesita un nombre, lo cual se realiza a través del llamado procedimiento de registro de nombres. Cada host reserva su nombre de host en la red o se utiliza el **NetBIOS Name Server (NBNS)** para este propósito. También se ha mejorado a **Windows Internet Name Service (WINS)**.

## Configuración Predeterminada

Como podemos imaginar, **Samba** ofrece una amplia gama de configuraciones que podemos ajustar. Estas configuraciones se definen a través de un archivo de texto donde podemos obtener una visión general de algunos de los ajustes. Estos ajustes lucen de la siguiente manera cuando se filtran:

```bash
sherlock28@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;"
```

### **Configuración Predeterminada de SMB**

```ini
[global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
```

Vemos configuraciones globales y dos recursos compartidos destinados a impresoras. Las configuraciones globales son la configuración del servidor SMB disponible que se utiliza para todos los recursos compartidos. En los recursos compartidos individuales, sin embargo, las configuraciones globales pueden ser sobrescritas, lo que puede configurarse con alta probabilidad de manera incorrecta. Veamos algunos de los ajustes para entender cómo se configuran los recursos compartidos en Samba.

### Configuraciones

| **Configuración**              | **Descripción**                                                                             |
| ------------------------------ | ------------------------------------------------------------------------------------------- |
| `[sharename]`                  | El nombre del recurso compartido en la red.                                                 |
| `workgroup = WORKGROUP/DOMAIN` | Grupo de trabajo que aparecerá cuando los clientes consulten.                               |
| `path = /path/here/`           | El directorio al que se debe dar acceso al usuario.                                         |
| `server string = STRING`       | La cadena que aparecerá cuando se inicie una conexión.                                      |
| `unix password sync = yes`     | ¿Sincronizar la contraseña UNIX con la contraseña SMB?                                      |
| `usershare allow guests = yes` | ¿Permitir que usuarios no autenticados accedan al recurso compartido definido?              |
| `map to guest = bad user`      | ¿Qué hacer cuando una solicitud de inicio de sesión no coincide con un usuario UNIX válido? |
| `browseable = yes`             | ¿Debería mostrarse este recurso compartido en la lista de recursos disponibles?             |
| `guest ok = yes`               | ¿Permitir conectar al servicio sin usar una contraseña?                                     |
| `read only = yes`              | ¿Permitir que los usuarios solo lean archivos?                                              |
| `create mask = 0700`           | ¿Qué permisos deben asignarse a los archivos recién creados?                                |

## Configuraciones Peligrosas

Algunas de las configuraciones anteriores ya presentan opciones sensibles. Sin embargo, si cuestionamos las siguientes configuraciones y analizamos lo que los empleados y los atacantes podrían obtener de ellas, veremos las ventajas y desventajas que presentan. Tomemos como ejemplo la configuración `browseable = yes`. Si los administradores adoptan esta configuración, los empleados de la empresa tendrán la comodidad de poder ver las carpetas individuales con el contenido. Muchas carpetas se usan eventualmente para una mejor organización y estructura. Si el empleado puede explorar los recursos compartidos, el atacante también podrá hacerlo después de obtener acceso exitoso.

| **Configuración**           | **Descripción**                                                             |
| --------------------------- | --------------------------------------------------------------------------- |
| `browseable = yes`          | ¿Permitir listar los recursos compartidos disponibles en el recurso actual? |
| `read only = no`            | ¿Prohibir la creación y modificación de archivos?                           |
| `writable = yes`            | ¿Permitir a los usuarios crear y modificar archivos?                        |
| `guest ok = yes`            | ¿Permitir conectar al servicio sin usar una contraseña?                     |
| `enable privileges = yes`   | ¿Honrar los privilegios asignados a SID específicos?                        |
| `create mask = 0777`        | ¿Qué permisos deben asignarse a los archivos recién creados?                |
| `directory mask = 0777`     | ¿Qué permisos deben asignarse a los directorios recién creados?             |
| `logon script = script.sh`  | ¿Qué script debe ejecutarse al iniciar sesión del usuario?                  |
| `magic script = script.sh`  | ¿Qué script debe ejecutarse cuando se cierre el script?                     |
| `magic output = script.out` | ¿Dónde se debe almacenar la salida del script mágico?                       |

Vamos a crear un recurso compartido llamado `[notes]` y algunos otros para ver cómo afectan nuestros procesos de enumeración. Usaremos todas las configuraciones anteriores y las aplicaremos a este recurso compartido. Por ejemplo, esta configuración se aplica a menudo, incluso solo con fines de prueba. Si se trata de una subred interna de un pequeño equipo en un gran departamento, esta configuración a menudo se conserva o se olvida reiniciar. Esto lleva a que podamos explorar todos los recursos compartidos y, con alta probabilidad, incluso descargarlos e inspeccionarlos.

### **Ejemplo de Recurso Compartido**

```ini
[notes]
   comment = CheckIT
   path = /mnt/notes/

   browseable = yes
   read only = no
   writable = yes
   guest ok = yes

   enable privileges = yes
   create mask = 0777
   directory mask = 0777
```

Es altamente recomendable consultar las páginas del manual de Samba y configurar por nosotros mismos y experimentar con los ajustes. Así descubriremos aspectos potencialmente interesantes para nosotros como testers de penetración. Además, cuanto más familiarizados estemos con el servidor Samba y SMB, más fácil será orientarnos en el entorno y utilizarlo para nuestros propósitos. Una vez que hayamos ajustado `/etc/samba/smb.conf` según nuestras necesidades, debemos reiniciar el servicio en el servidor.

### Reiniciar Samba

Para reiniciar el servicio Samba, ejecutamos el siguiente comando:

```bash
root@samba:~# sudo systemctl restart smbd
```

Ahora podemos mostrar una lista (-L) de los recursos compartidos del servidor usando el comando `smbclient` desde nuestro host. Utilizamos la llamada sesión nula (-N), que es un acceso anónimo sin la necesidad de introducir usuarios existentes o contraseñas válidas.

### Conexión al Recurso Compartido con SMBclient

```bash
sherlock28@htb[/htb]$ smbclient -N -L //10.129.14.128
```

**Resultado:**

```mathematica
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```

Vemos que ahora tenemos cinco recursos compartidos diferentes en el servidor Samba. `print$` e `IPC$` están incluidos por defecto en la configuración básica, como ya hemos visto. Dado que estamos tratando con el recurso compartido `[notes]`, iniciemos sesión e inspeccionemos el recurso usando el mismo programa cliente. Si no estamos familiarizados con el programa cliente, podemos usar el comando `help` después de iniciar sesión con éxito, para listar todos los comandos posibles que podemos ejecutar.

#### Conexión al Recurso Compartido `[notes]`

```bash
sherlock28@htb[/htb]$ smbclient //10.129.14.128/notes
```

**Resultado:**

```vbnet
vbnetCopiar códigoEnter WORKGROUP\<username>'s password: 
Anonymous login successful
Try "help" to get a list of possible commands.
```

**Comandos disponibles:**

```bash
smb: \> help

?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!
```

Para ver el contenido del recurso compartido, usamos el comando `ls`:

```bash
smb: \> ls
```

**Resultado:**

```yaml
  .                                   D        0  Wed Sep 22 18:17:51 2021
  ..                                  D        0  Wed Sep 22 12:03:59 2021
  prep-prod.txt                       N       71  Sun Sep 19 15:45:21 2021

                30313412 blocks of size 1024. 16480084 blocks available
```

Una vez que descubrimos archivos o carpetas interesantes, podemos descargarlos usando el comando `get`. `smbclient` también permite ejecutar comandos del sistema local usando un signo de exclamación al principio (!\<cmd>) sin interrumpir la conexión.

### Descargar Archivos desde SMB

```bash
smb: \> get prep-prod.txt 
```

**Resultado:**

```bash
getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec) 
(average 8,7 KiloBytes/sec)
```

Para verificar los archivos descargados localmente:

```bash
smb: \> !ls
```

**Resultado:**

```
prep-prod.txt
```

Para ver el contenido del archivo:

```bash
smb: \> !cat prep-prod.txt
```

**Resultado:**

```css
[] check your code with the templates
[] run code-assessment.py
[] …
```

Desde el punto de vista administrativo, podemos verificar estas conexiones usando `smbstatus`. Además de la versión de Samba, también podemos ver quién, desde qué host y a qué recurso compartido está conectado el cliente. Esto es especialmente importante una vez que hemos ingresado a una subred (quizás incluso una aislada) a la que otros aún pueden acceder.

### Estado de Samba

```bash
root@samba:~# smbstatus
```

**Resultado:**

```markdown
Samba version 4.11.6-Ubuntu
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing              
----------------------------------------------------------------------------------------------------------------------------------------
75691   sambauser    samba        10.10.14.4 (ipv4:10.10.14.4:45564)      SMB3_11           -                    -                    

Service      pid     Machine       Connected at                     Encryption   Signing     
---------------------------------------------------------------------------------------------
notes        75691   10.10.14.4   Do Sep 23 00:12:06 2021 CEST     -            -           

No locked files
```

## Identificación del Servicio/Footprinting the service

Volvamos a una de nuestras herramientas de enumeración. Nmap también tiene muchas opciones y scripts NSE que pueden ayudarnos a examinar el servicio SMB del objetivo con más detalle y obtener más información. Sin embargo, la desventaja es que estos escaneos pueden tardar mucho tiempo. Por lo tanto, también se recomienda revisar el servicio manualmente, principalmente porque podemos encontrar muchos más detalles de los que Nmap podría mostrar. Primero, veamos qué puede encontrar Nmap en nuestro servidor Samba, donde creamos el recurso compartido `[notes]` para fines de prueba.

### Nmap

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445
```

**Resultado:**

```less
Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for sharing.inlanefreight.htb (10.129.14.128)
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.35 seconds
```

Podemos ver que los resultados de Nmap no ofrecen mucha información. Por lo tanto, deberíamos recurrir a otras herramientas que nos permitan interactuar manualmente con el SMB y enviar solicitudes específicas para obtener información. Una de las herramientas útiles para esto es `rpcclient`, que es una herramienta para realizar funciones MS-RPC.

### RPCclient

El Remote Procedure Call (RPC) es un concepto y también una herramienta central para realizar estructuras operativas y de trabajo en redes y arquitecturas cliente-servidor. El proceso de comunicación a través de RPC incluye el paso de parámetros y el retorno de un valor de función.

Para usar `rpcclient`, ejecutamos el siguiente comando:

```bash
sherlock28@htb[/htb]$ rpcclient -U "" 10.129.14.128
```

**Resultado:**

```mathematica
Enter WORKGROUP\'s password:
rpcclient $> 
```

El `rpcclient` nos ofrece muchas solicitudes diferentes con las que podemos ejecutar funciones específicas en el servidor SMB para obtener información. A continuación, una lista de algunas de estas funciones:

**Consultas**

| Consulta                  | Descripción                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------- |
| `srvinfo`                 | Información del servidor.                                                              |
| `enumdomains`             | Enumera todos los dominios desplegados en la red.                                      |
| `querydominfo`            | Proporciona información sobre dominio, servidor y usuario de los dominios desplegados. |
| `netshareenumall`         | Enumera todos los recursos compartidos disponibles.                                    |
| `netsharegetinfo <share>` | Proporciona información sobre un recurso compartido específico.                        |
| `enumdomusers`            | Enumera todos los usuarios del dominio.                                                |
| `queryuser <RID>`         | Proporciona información sobre un usuario específico.                                   |

### Enumeración con RPCclient

```bash
rpcclient $> srvinfo
```

**Resultado:**

```yaml
        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
```

```bash
rpcclient $> enumdomains
```

**Resultado:**

```makefile
name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]
```

```bash
rpcclient $> querydominfo
```

**Resultado:**

```yaml
Domain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1
```

```bash
rpcclient $> netshareenumall
```

**Resultado:**

```yaml
netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:
```

```bash
rpcclient $> netsharegetinfo notes
```

**Resultado:**

```yaml
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00 
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
                SID: S-1-1-0
```

Estos ejemplos muestran qué información puede ser filtrada a usuarios anónimos. Una vez que un usuario anónimo tiene acceso a un servicio de red, solo se necesita un error para darle demasiados permisos o demasiada visibilidad y poner toda la red en un riesgo significativo.

Lo más importante es que el acceso anónimo a tales servicios también puede llevar al descubrimiento de otros usuarios, quienes pueden ser atacados con fuerza bruta en el caso más agresivo. Los humanos son más propensos a cometer errores que los procesos informáticos correctamente configurados, y la falta de conciencia de seguridad y la pereza a menudo conducen a contraseñas débiles que pueden ser fácilmente descifradas. Veamos cómo podemos enumerar usuarios usando `rpcclient`.

### Enumeración de Usuarios con Rpcclient

Para enumerar los usuarios usando `rpcclient`, se utilizan los siguientes comandos:

```bash
rpcclient $> enumdomusers
```

**Resultado:**

```makefile
user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]
```

Para obtener más detalles sobre un usuario específico, usamos el comando `queryuser <RID>`:

```bash
rpcclient $> queryuser 0x3e9
```

**Resultado:**

```yaml
yamlCopiar códigoUser Name   :   cry0l1t3
Full Name   :   cry0l1t3
Home Drive  :   \\devsmb\cry0l1t3
Dir Drive   :
Profile Path:   \\devsmb\cry0l1t3\profile
Logon Script:
Description :
Workstations:
Comment     :
Remote Dial :
Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
unknown_2[0..31]...
user_rid :      0x3e9
group_rid:      0x201
acb_info :      0x00000014
fields_present: 0x00ffffff
logon_divs:     168
bad_password_count:     0x00000000
logon_count:    0x00000000
padding1[0..7]...
logon_hrs[0..21]...
```

```bash
rpcclient $> queryuser 0x3e8
```

**Resultado:**

```yaml
User Name   :   mrb3n
Full Name   :
Home Drive  :   \\devsmb\mrb3n
Dir Drive   :
Profile Path:   \\devsmb\mrb3n\profile
Logon Script:
Description :
Workstations:
Comment     :
Remote Dial :
Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
Password last set Time   :      Mi, 22 Sep 2021 17:47:59 CEST
Password can change Time :      Mi, 22 Sep 2021 17:47:59 CEST
Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
unknown_2[0..31]...
user_rid :      0x3e8
group_rid:      0x201
acb_info :      0x00000010
fields_present: 0x00ffffff
logon_divs:     168
bad_password_count:     0x00000000
logon_count:    0x00000000
padding1[0..7]...
logon_hrs[0..21]...
```

Para obtener información sobre el grupo al que pertenecen los usuarios, usamos el siguiente comando:

```bash
rpcclient $> querygroup 0x201
```

**Resultado:**

```sql
Group Name:     None
Description:    Ordinary Users
Group Attribute:7
Num Members:2
```

### Fuerza Bruta de RIDs de Usuario

Si no conocemos los RIDs asignados, podemos realizar una fuerza bruta de los RIDs para obtener información. Aquí hay un ejemplo usando un bucle en Bash:

```bash
sherlock28@htb[/htb]$ for i in $(seq 500 1100); do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo ""; done
```

**Resultado:**

```yaml
User Name   :   sambauser
user_rid :      0x1f5
group_rid:      0x201

User Name   :   mrb3n
user_rid :      0x3e8
group_rid:      0x201

User Name   :   cry0l1t3
user_rid :      0x3e9
group_rid:      0x201
```

### Alternativas y Herramientas Adicionales

#### **Impacket - `samrdump.py`**

Otra herramienta útil es el script `samrdump.py` de Impacket, que puede proporcionar información de usuarios:

```bash
sherlock28@htb[/htb]$ samrdump.py 10.129.14.128
```

**Resultado:**

```yaml
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
mrb3n (1000)/FullName: 
mrb3n (1000)/UserComment: 
mrb3n (1000)/PrimaryGroupId: 513
mrb3n (1000)/BadPasswordCount: 0
mrb3n (1000)/LogonCount: 0
mrb3n (1000)/PasswordLastSet: 2021-09-22 17:47:59
mrb3n (1000)/PasswordDoesNotExpire: False
mrb3n (1000)/AccountIsDisabled: False
mrb3n (1000)/ScriptPath: 
cry0l1t3 (1001)/FullName: cry0l1t3
cry0l1t3 (1001)/UserComment: 
cry0l1t3 (1001)/PrimaryGroupId: 513
cry0l1t3 (1001)/BadPasswordCount: 0
cry0l1t3 (1001)/LogonCount: 0
cry0l1t3 (1001)/PasswordLastSet: 2021-09-22 17:50:56
cry0l1t3 (1001)/PasswordDoesNotExpire: False
cry0l1t3 (1001)/AccountIsDisabled: False
cry0l1t3 (1001)/ScriptPath: 
[*] Received 2 entries.
```

**SMBMap**

Para enumerar los recursos compartidos y sus permisos:

```bash
sherlock28@htb[/htb]$ smbmap -H 10.129.14.128
```

**Resultado:**

```yaml
[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)
```

**CrackMapExec**

Para enumerar los recursos compartidos con CrackMapExec:

```bash
sherlock28@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```

**Resultado:**

```less
SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \: 
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           print$                          Printer Drivers
SMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)
```

### Herramienta Adicional: Enum4linux-ng

Una herramienta adicional que vale la pena mencionar es `enum4linux-ng`, que automatiza muchas de las consultas y puede proporcionar una gran cantidad de información sobre servicios SMB.

#### Instalación de Enum4Linux-ng

Para instalar `enum4linux-ng`, sigue estos pasos:

```bash
sherlock28@htb[/htb]$ git clone https://github.com/cddmp/enum4linux-ng.git
sherlock28@htb[/htb]$ cd enum4linux-ng
sherlock28@htb[/htb]$ pip3 install -r requirements.txt
```

#### Enumeración con Enum4Linux-ng

Para realizar una enumeración completa, utiliza el siguiente comando:

```bash
sherlock28@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -A
```

**Resultado:**

```yaml
ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.14.128
[*] Username ......... ''
[*] Random Username .. 'juzgtcsu'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Service Scan on 10.129.14.128    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    NetBIOS Names and Workgroup for 10.129.14.128    |
 =====================================================
[+] Got domain/workgroup name: DEVOPS
[+] Full NetBIOS names information:
- DEVSMB          <00> -         H <ACTIVE>  Workstation Service
- DEVSMB          <03> -         H <ACTIVE>  Messenger Service
- DEVSMB          <20> -         H <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE>  Master Browser
- DEVOPS          <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
- DEVOPS          <1d> -         H <ACTIVE>  Master Browser
- DEVOPS          <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 ==========================================
|    SMB Dialect Check on 10.129.14.128    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
SMB 1.0: false
SMB 2.02: true
SMB 2.1: true
SMB 3.0: true
SMB1 only: false
Preferred dialect: SMB 3.0
SMB signing required: false

 ==========================================
|    RPC Session Check on 10.129.14.128    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'juzgtcsu', password ''
[H] Rerunning enumeration with user 'juzgtcsu' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.14.128    |
 ====================================================
[+] Domain: DEVOPS
[+] SID: NULL SID
[+] Host is part of a workgroup (not a domain)

 ============================================================
|    Domain Information via SMB session for 10.129.14.128    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DEVSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: htb

 ================================================
|    OS Information via RPC for 10.129.14.128    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 7, Windows Server 2008 R2
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: not supported
Native LAN manager: not supported
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT DEVSM

 ======================================
|    Users via RPC on 10.129.14.128    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 2 users via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 2 users via 'enumdomusers'
[+] After merging user results we have 2 users total:
'1000':
  username: mrb3n
  name: ''
  acb: '0x00000010'
  description: ''
'1001':
  username: cry0l1t3
  name: cry0l1t3
  acb: '0x00000014'
  description: ''

 =======================================
|    Groups via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 =======================================
|    Shares via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating shares
[+] Found 5 share(s):
IPC$:
  comment: IPC Service (DEVSM)
  type: IPC
dev:
  comment: DEVenv
  type: Disk
home:
  comment: INFREIGHT Samba
  type: Disk
notes:
  comment: CheckIT
  type: Disk
print$:
  comment: Printer Drivers
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share dev
[-] Share doesn't exist
[*] Testing share home
[+] Mapping: OK, Listing: OK
[*] Testing share notes
[+] Mapping: OK, Listing: OK
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A

 ==========================================
|    Policies via RPC for 10.129.14.128    |
 ==========================================
[*] Trying port 445/tcp
[+] Found policy:
domain_password_information:
  pw_history_length: None
  min_pw_length: 5
  min_pw_age: none
  max_pw_age: 49710 days 6 hours 21 minutes
  pw_properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
domain_lockout_information:
  lockout_observation_window: 30 minutes
  lockout_duration: 30 minutes
  lockout_threshold: None
domain_logoff_information:
  force_logoff_time: 49710 days 6 hours 21 minutes

 ==========================================
|    Printers via RPC for 10.129.14.128    |
 ==========================================
[+] No printers returned (this is not an error)

Completed after 0.61 seconds
```

> **Nota**: Es importante usar más de una herramienta para la enumeración, ya que puede suceder que diferentes herramientas proporcionen información variada. Por lo tanto, no debes confiar únicamente en herramientas automatizadas sin comprender cómo están programadas.
