---
description: '🔍 Dificultad: Principiante'
---

# Microchoft

### 🔍 **RECONOCIMIENTO**

En primer lugar, miramos la **IP** de la máquina víctima con este comando:

```bash
sudo arp-scan --interface=eth0 --localnet
```

<figure><img src="../../.gitbook/assets/imagen (15).png" alt=""><figcaption></figcaption></figure>

En segundo lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.57
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/imagen (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Definitivamente es una máquina Windows porque el TTL=128.

A continuación, se realiza el comando:

```bash
sudo nmap -p- --open -sS -Pn --min-rate 5000 -n 192.168.1.57
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/imagen (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los siguientes puertos están abiertos:

* **135/tcp**: Pertenece al servicio **MSRPC** (Microsoft Remote Procedure Call), que es utilizado para la comunicación entre procesos en sistemas Windows.
* **139/tcp**: Pertenece al servicio **NetBIOS-SSN** (NetBIOS Session Service), utilizado por redes que ejecutan el protocolo SMB (Server Message Block) en sistemas Windows.
* **445/tcp**: Pertenece al servicio **Microsoft-DS** (Microsoft Directory Services), utilizado para compartir archivos en redes Windows mediante el protocolo SMB.

Además, se han detectado varios puertos en el rango dinámico de Windows (49152-65535) abiertos, pero sin un servicio conocido específico:

* **49152/tcp - 49157/tcp**: Puertos marcados como **unknown**, los cuales pueden estar relacionados con servicios de red personalizados o asignados dinámicamente por el sistema operativo para uso de aplicaciones específicas.

Dado que los puertos **135/tcp**, **139/tcp**, y **445/tcp** están abiertos, se indagará más en relación a estos puertos para evaluar posibles vulnerabilidades o configuraciones incorrectas en los servicios SMB y MSRPC, que son comúnmente utilizados en entornos Windows y pueden ser potenciales vectores de ataque si no están adecuadamente asegurados.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p135,139,445 -v 192.168.1.57
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/imagen (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **445/tcp** perteneciente al servicio **Microsoft-DS** está abierto. Este puerto es utilizado para compartir archivos y otros recursos en redes Windows mediante el protocolo SMB (Server Message Block).

Al analizar más a fondo, se ha detectado que el sistema operativo es **Windows 7 Home Basic SP1 (Service Pack 1)** y está configurado en el grupo de trabajo **WORKGROUP**. Este sistema, con el **puerto 445 abierto**, es particularmente preocupante ya que es vulnerable a la famosa vulnerabilidad **EternalBlue**.

**EternalBlue**, que afecta versiones de Windows no actualizadas, explota una vulnerabilidad crítica en el protocolo SMBv1, lo que podría permitir la ejecución remota de código y, en consecuencia, el control completo del sistema afectado. Esta vulnerabilidad ha sido utilizada en ataques masivos como el ransomware **WannaCry**.

Dado que estamos frente a un sistema operativo obsoleto (Windows 7), y que el puerto **445/tcp** está expuesto, se procederá a indagar más para determinar si el sistema es susceptible a esta vulnerabilidad y recomendar medidas de mitigación, como la actualización del sistema operativo o la desactivación del protocolo SMBv1.<br>

Por eso mismo revisamos si es vulnerable por si acaso con el comando:

```
nmap -p445 --script="vuln and safe" 192.168.1.57
```

<figure><img src="../../.gitbook/assets/imagen (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
msfconsole
```

<figure><img src="../../.gitbook/assets/imagen (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación se intentará explotar esa vulnerabilidad a través de la herramienta **Metasploit**. Se busca la vulnerabilidad exacta que obtenida en el anterior escaneo.

```bash
search ms17-010
```

<figure><img src="../../.gitbook/assets/imagen (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el segundo ya que tenemos un **Windows 7** de máquina víctima.

```bash
use 2
```

<figure><img src="../../.gitbook/assets/imagen (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Miramos las opciones que tenemos que configurar con el comando:

```bash
show options
```

<figure><img src="../../.gitbook/assets/imagen (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, configuramos los parámetros necesarios para realizar el ataque:

* **RHOSTS**: Definimos la dirección IP del host remoto (objetivo) como `192.168.1.57`.
* **LHOST**: Establecemos la dirección IP de nuestro host local (atacante) como `192.158.1.50`.

<figure><img src="../../.gitbook/assets/imagen (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

Después de haber configurado estos parámetros correctamente, procederemos a verificar si el objetivo es explotable. Esto implica ejecutar la explotación para confirmar si la vulnerabilidad está presente y si podemos obtener acceso o control del sistema remoto.

```bash
exploit
```

<figure><img src="../../.gitbook/assets/imagen (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro ejecutamos:

```bash
pwd
```

<figure><img src="../../.gitbook/assets/imagen (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

A medida que avanzamos en nuestra investigación, revisaremos los directorios de los usuarios **Admin** y **Lola** para encontrar las diferentes flags requeridas en este reto.

¡Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
