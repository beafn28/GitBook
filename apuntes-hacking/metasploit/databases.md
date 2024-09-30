# Databases

Las bases de datos en **msfconsole** se utilizan para hacer un seguimiento de los resultados. No es un misterio que durante evaluaciones de máquinas más complejas, o incluso de redes enteras, las cosas pueden volverse confusas y complicadas debido a la gran cantidad de resultados de búsqueda, puntos de entrada, problemas detectados, credenciales descubiertas, etc.

Aquí es donde entran en juego las bases de datos. **Msfconsole** tiene soporte incorporado para el sistema de bases de datos PostgreSQL. Con ello, tenemos acceso directo, rápido y fácil a los resultados de los escaneos, con la capacidad añadida de importar y exportar resultados en conjunto con herramientas de terceros. Las entradas de la base de datos también pueden utilizarse para configurar los parámetros de los módulos de explotación con los hallazgos ya existentes directamente.

## Configuración de la Base de Datos

Primero, debemos asegurarnos de que el servidor PostgreSQL esté en funcionamiento en nuestra máquina host. Para ello, introduce el siguiente comando:

### Estado de PostgreSQL

```bash
sherlock28@htb[/htb]$ sudo service postgresql status
```

### Iniciar PostgreSQL

```bash
sherlock28@htb[/htb]$ sudo systemctl start postgresql
```

Después de iniciar PostgreSQL, necesitamos crear e inicializar la base de datos de MSF con `msfdb init`.

### MSF - Iniciar una Base de Datos

```bash
sherlock28@htb[/htb]$ sudo msfdb init
```

```plaintext
[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
rake aborted!
NoMethodError: undefined method `without' for #<Bundler::Settings:0x000055dddcf8cba8>
Did you mean? with_options

<SNIP>
```

A veces, puede ocurrir un error si Metasploit no está actualizado. Esta diferencia que causa el error puede suceder por varias razones. A menudo, ayuda actualizar Metasploit nuevamente (`apt update`) para resolver este problema. Luego, podemos intentar reinicializar la base de datos de MSF.

```bash
sherlock28@htb[/htb]$ sudo msfdb init
```

```plaintext
[i] Database already started
[i] The database appears to be already configured, skipping initialization
```

Si la inicialización se omite y Metasploit nos informa que la base de datos ya está configurada, podemos volver a verificar el estado de la base de datos.

```bash
sherlock28@htb[/htb]$ sudo msfdb status
```

```plaintext
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
     Active: active (exited) since Mon 2022-05-09 15:19:57 BST; 35min ago
    Process: 2476 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2476 (code=exited, status=0/SUCCESS)
        CPU: 1ms

May 09 15:19:57 pwnbox-base systemd[1]: Starting PostgreSQL RDBMS...
May 09 15:19:57 pwnbox-base systemd[1]: Finished PostgreSQL RDBMS.

COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
postgres 2458 postgres    5u  IPv6  34336      0t0  TCP localhost:5432 (LISTEN)
postgres 2458 postgres    6u  IPv4  34337      0t0  TCP localhost:5432 (LISTEN)

UID          PID    PPID  C STIME TTY      STAT   TIME CMD
postgres    2458       1  0 15:19 ?        Ss     0:00 /usr/lib/postgresql/13/bin/postgres -D /var/lib/postgresql/13/main -c con

[+] Detected configuration file (/usr/share/metasploit-framework/config/database.yml)
```

Si este error no aparece, lo que a menudo sucede después de una instalación nueva de Metasploit, entonces veremos lo siguiente al inicializar la base de datos:

```bash
sherlock28@htb[/htb]$ sudo msfdb init
```

```plaintext
[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
```

Después de que la base de datos haya sido inicializada, podemos iniciar **msfconsole** y conectarnos a la base de datos creada simultáneamente.

### MSF - Conectar a la Base de Datos Iniciada

```bash
sherlock28@htb[/htb]$ sudo msfdb run
```

```plaintext
[i] Database already started
                                                  

         .                                         .
 .

      dBBBBBBb  dBBBP dBBBBBBP dBBBBBb  .                       o
       '   dB'                     BBP
    dB'dB'dB' dBBP     dBP     dBP BB
   dB'dB'dB' dBP      dBP     dBP  BB
  dB'dB'dB' dBBBBP   dBP     dBBBBBBB

                                   dBBBBBP  dBBBBBb  dBP    dBBBBP dBP dBBBBBBP
          .                  .                  dB' dBP    dB'.BP
                             |       dBP    dBBBB' dBP    dB'.BP dBP    dBP
                           --o--    dBP    dBP    dBP    dB'.BP dBP    dBP
                             |     dBBBBP dBP    dBBBBP dBBBBP dBP    dBP

                                                                    .
                .
        o                  To boldly go where no
                            shell has gone before


       =[ metasploit v6.1.39-dev                          ]
+ -- --=[ 2214 exploits - 1171 auxiliary - 396 post       ]
+ -- --=[ 616 payloads - 45 encoders - 11 nops            ]
+ -- --=[ 9 evasion                                       ]

msf6>
```

Sin embargo, si ya tenemos la base de datos configurada y no podemos cambiar la contraseña del usuario de MSF, procedemos con los siguientes comandos:

### MSF - Reinitiar la Base de Datos

```bash
sherlock28@htb[/htb]$ msfdb reinit
sherlock28@htb[/htb]$ cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/
sherlock28@htb[/htb]$ sudo service postgresql restart
sherlock28@htb[/htb]$ msfconsole -q
```

#### Comprobando el Estado de la Base de Datos

```bash
msf6 > db_status
```

```plaintext
[*] Connected to msf. Connection type: PostgreSQL.
```

Ahora estamos listos para continuar. **Msfconsole** también ofrece ayuda integrada para la base de datos, lo que nos brinda una buena visión general de cómo interactuar y usar la base de datos.

### MSF - Opciones de Base de Datos

```bash
msf6 > help database
```

```plaintext
Database Backend Commands
=========================

    Command           Description
    -------           ----------- 
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces
```

#### Comprobando el Estado de la Base de Datos

```bash
msf6 > db_status
```

```plaintext
[*] Connected to msf. Connection type: postgresql.
```

## Usando la Base de Datos

Con la ayuda de la base de datos, podemos gestionar muchas categorías diferentes y hosts que hemos analizado. Alternativamente, la información sobre ellos que hemos interactuado usando Metasploit. Estas bases de datos pueden ser exportadas e importadas. Esto es especialmente útil cuando tenemos listas extensas de hosts, loot, notas y vulnerabilidades almacenadas para estos hosts. Después de confirmar que la base de datos está conectada correctamente, podemos organizar nuestros Workspaces.

### Workspaces

Podemos pensar en los Workspaces de la misma manera que pensamos en carpetas en un proyecto. Podemos segregar los diferentes resultados de escaneo, hosts e información extraída por IP, subred, red o dominio.

Para ver la lista actual de Workspaces, usa el comando `workspace`. Agregar un switch `-a` o `-d` después del comando, seguido del nombre del workspace, añadirá o eliminará ese workspace de la base de datos.

```bash
msf6 > workspace
```

```plaintext
código* default
```

Nota que el Workspace predeterminado se llama `default` y está actualmente en uso según el símbolo `*`. Escribe el comando `workspace [nombre]` para cambiar al workspace actualmente usado. Mirando nuestro ejemplo, creemos un workspace para esta evaluación y seleccionémoslo.

```bash
msf6 > workspace -a Target_1
```

```plaintext
[*] Added workspace: Target_1
[*] Workspace: Target_1
```

```bash
msf6 > workspace Target_1 
```

```plaintext
[*] Workspace: Target_1
```

```bash
msf6 > workspace
```

```plaintext
default
* Target_1
```

Para ver qué más podemos hacer con los Workspaces, podemos usar el comando `workspace -h` para el menú de ayuda relacionado con los Workspaces.

```bash
msf6 > workspace -h
```

```plaintext
Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r     Rename workspace
    workspace -h               Show this help information
```

## Importando Resultados de Escaneo

A continuación, supongamos que queremos importar un escaneo de Nmap de un host a nuestro Workspace de la base de datos para comprender mejor el objetivo. Podemos usar el comando `db_import` para esto. Después de que la importación se complete, podemos comprobar la presencia de la información del host en nuestra base de datos utilizando los comandos `hosts` y `services`. Ten en cuenta que el tipo de archivo `.xml` es preferido para `db_import`.

### Escaneo Nmap Almacenado

```bash
sherlock28@htb[/htb]$ cat Target.nmap
```

```plaintext
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 20:54 UTC
Nmap scan report for 10.10.10.40
Host is up (0.017s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.81 seconds
```

### Importando Resultados de Escaneo

```bash
msf6 > db_import Target.xml
```

```plaintext
[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.10.9'
[*] Importing host 10.10.10.40
[*] Successfully imported ~/Target.xml
```

```bash
msf6 > hosts
```

```plaintext
Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.40             Unknown                    device          
```

```bash
msf6 > services
```

```plaintext
Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.10.10.40  135    tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.10.10.40  445    tcp    microsoft-ds  open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
10.10.10.40  49152  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49153  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49154  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49155  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49156  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49157  tcp    msrpc         open   Microsoft Windows RPC
```

## Usando Nmap Dentro de MSFconsole

Alternativamente, podemos usar Nmap directamente desde `msfconsole`. Para escanear directamente desde la consola sin tener que poner en segundo plano o salir del proceso, utiliza el comando `db_nmap`.

### Comando de Nmap

```bash
msf6 > db_nmap -sV -sS 10.10.10.8
```

```less
[*] Nmap: Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 21:04 UTC
[*] Nmap: Nmap scan report for 10.10.10.8
[*] Nmap: Host is up (0.016s latency).
[*] Nmap: Not shown: 999 filtered ports
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 80/TCP open  http    HttpFileServer httpd 2.3
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ 
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 11.12 seconds
```

Después de realizar el escaneo, puedes listar los hosts y servicios descubiertos:

```bash
msf6 > hosts
```

```markdown
Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.8              Unknown                    device         
10.10.10.40             Unknown                    device         
```

```bash
msf6 > services
```

```lua
Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.10.10.8   80     tcp    http          open   HttpFileServer httpd 2.3
10.10.10.40  135    tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.10.10.40  445    tcp    microsoft-ds  open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
10.10.10.40  49152  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49153  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49154  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49155  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49156  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49157  tcp    msrpc         open   Microsoft Windows RPC
```

## Copia de Seguridad de Datos

Después de finalizar la sesión, asegúrate de hacer una copia de seguridad de nuestros datos en caso de que ocurra algo con el servicio PostgreSQL. Para hacerlo, utiliza el comando `db_export`.

```bash
msf6 > db_export -h
```

```yaml
Usage:
    db_export -f <format> [filename]
    Format can be one of: xml, pwdump
[-] No output file was specified
```

```bash
msf6 > db_export -f xml backup.xml
```

```css
[*] Starting export of workspace default to backup.xml [ xml ]...
[*] Finished export of workspace default to backup.xml [ xml ]...
```

Esta información puede ser importada nuevamente en `msfconsole` cuando sea necesario.

## Hosts

El comando `hosts` muestra una tabla de base de datos que se llena automáticamente con las direcciones de los hosts, nombres de host y otra información que encontramos durante nuestros escaneos e interacciones.

```bash
msf6 > hosts -h
```

```plaintext
hosts [ options ] [addr1 addr2 ...]
```

## Servicios

El comando `services` funciona de manera similar al anterior y contiene una tabla con descripciones e información sobre los servicios descubiertos durante escaneos o interacciones.

```bash
msf6 > services -h
```

```plaintext
services [-h] [-u] [-a] [-r <proto>] [-p <port1,port2>] [-s <name1,name2>] [-o <filename>] [addr1 addr2 ...]
```

## Credenciales

El comando `creds` permite visualizar las credenciales recopiladas durante nuestras interacciones con el host objetivo.

```bash
msf6 > creds -h
```

```plaintext
creds [filter options] [address range]
```

## Loot

El comando `loot` trabaja en conjunto con el comando anterior para ofrecer una lista de servicios y usuarios comprometidos. Loot se refiere a volcado de hashes de diferentes tipos de sistema, incluidos hashes, passwd, shadow, entre otros.

```bash
msf6 > loot -h
```

```plaintext
loot [options]
```
