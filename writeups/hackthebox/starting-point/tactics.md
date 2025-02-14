# Tactics

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Protocols (Protocolos):** Los protocolos son un conjunto de reglas que permiten la comunicación entre dispositivos en una red. Algunos protocolos, como SMB o FTP, pueden ser objetivos comunes para atacantes si no están configurados adecuadamente. La comprensión de los protocolos es esencial en pruebas de penetración para identificar posibles vectores de ataque.
  * **SMB (Server Message Block):** SMB es un protocolo de red utilizado para compartir archivos e impresoras en entornos Windows. Si no se configura correctamente, SMB puede ser vulnerable a ataques como el acceso no autenticado, la captura de credenciales o la explotación de vulnerabilidades críticas, como EternalBlue.
  * **Reconnaissance (Reconocimiento):** Es la fase inicial de una prueba de penetración donde se recopila información sobre el objetivo. Se pueden utilizar técnicas como escaneo de puertos, identificación de servicios en ejecución y análisis de configuraciones para descubrir posibles puntos débiles. Un reconocimiento efectivo permite detectar servicios mal configurados o vulnerabilidades explotables.
  * **Misconfiguration (Mala Configuración):** Se refiere a errores en la configuración de sistemas y servicios que pueden llevar a vulnerabilidades explotables. Una mala configuración puede incluir credenciales predeterminadas, permisos excesivos, exposición innecesaria de servicios o falta de cifrado adecuado. Muchas brechas de seguridad ocurren debido a errores de configuración en lugar de fallos en el software.&#x20;

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.40.74
```

<figure><img src="../../../.gitbook/assets/image (1202).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Windows** ya que su **ttl=127**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sC -Pn 10.129.40.74
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (1203).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo los puertos **135**, **139** y **445**, que están relacionados con los servicios **RPC** y **SMB**, también se encuentran abiertos. A continuación, se indagará más sobre estos servicios.

Listamos los recursos compartidos.

```bash
smbclient -L //10.129.40.74/ -U Administrator
```

<figure><img src="../../../.gitbook/assets/image (1204).png" alt=""><figcaption></figcaption></figure>

Entramos en el recurso compartido `C$`.

```bash
smbclient //10.129.40.74/C$ -U Administrator
```

<figure><img src="../../../.gitbook/assets/image (1205).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (1206).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1207).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Tarea 1**

¿Qué opción de Nmap podemos usar para enumerar máquinas cuando nuestros paquetes ICMP de ping están bloqueados por el firewall de Windows?\
**Respuesta**: -Pn

**Tarea 2**

¿Qué significa el acrónimo de 3 letras SMB?\
**Respuesta**: Server Message Block

**Tarea 3**

¿Qué puerto utiliza SMB para operar?\
**Respuesta**: 445

**Tarea 4**

¿Qué argumento de línea de comandos le das a `smbclient` para listar los recursos compartidos disponibles?\
**Respuesta**: -L

**Tarea 5**

¿Qué carácter al final de un nombre de recurso compartido indica que es un recurso compartido administrativo?\
**Respuesta**: $

**Tarea 6**

¿Qué recurso compartido administrativo está accesible en la máquina que permite a los usuarios ver todo el sistema de archivos?\
**Respuesta**: C$

**Tarea 7**

¿Qué comando podemos usar para descargar los archivos que encontramos en el recurso compartido SMB?\
**Respuesta**: get

**Tarea 8**

¿Qué herramienta que es parte de la colección de Impacket se puede usar para obtener una shell interactiva en el sistema?\
**Respuesta**: psexec.py
