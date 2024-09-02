# Meow

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Telnet**: Se enfoca en el servicio utilizado.
  * **Protocolos**: Relacionado con los protocolos involucrados.
  * **Reconocimiento**: Proceso de exploración y escaneo.
  * **Credenciales Débiles**: Identificación de credenciales inseguras.
  * **Configuración Incorrecta**: Problemas en la configuración del sistema.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.148.147
```

<figure><img src="../../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap 10.129.148.147
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **23** perteneciendo al servicio **Telnet** está abierto, por lo que se procederá a indagar más.

<figure><img src="../../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Tras estar dentro listamos y encontramos un archivo llamado`flag.txt` que deberá mostrar la flag que nos preguntan.

<figure><img src="../../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: ¿Qué significa el acrónimo VM?

**Máquina Virtual**

_Una Máquina Virtual (VM) es un entorno virtual que funciona como un sistema informático virtual con su propio CPU, memoria, interfaz de red y almacenamiento, creado en un sistema de hardware físico. El hipervisor separa todos los recursos de la máquina del hardware y los proporciona adecuadamente para que puedan ser utilizados por la VM. La tecnología de virtualización permite a los usuarios compartir un sistema con entornos virtuales._

#### Tarea 2: ¿Qué herramienta usamos para interactuar con el sistema operativo y emitir comandos a través de la línea de comandos, como el de iniciar nuestra conexión VPN? También se conoce como consola o shell.

**Terminal**

_La Terminal es una interfaz basada en texto utilizada para controlar una computadora Linux ejecutando comandos específicos con la entrada adecuada._

#### Tarea 3: ¿Qué servicio utilizamos para formar nuestra conexión VPN en los laboratorios de HTB?

**openvpn**

_OpenVPN es un protocolo VPN de código abierto que utiliza técnicas de red privada virtual (VPN) para establecer conexiones seguras entre sitios o punto a punto._

#### Tarea 4: ¿Qué herramienta utilizamos para probar nuestra conexión con el objetivo mediante una solicitud de eco ICMP?

**Ping**

_Ping (Packet Internet Groper) se invoca utilizando el comando "ping", que usa ICMP (Internet Control Message Protocol) para informar errores y proporcionar información relacionada con el procesamiento de paquetes IP. Ping funciona enviando un mensaje de solicitud de eco ICMP a la dirección IP proporcionada. Si la computadora con la dirección IP de destino es alcanzable, responde con un mensaje de respuesta de eco ICMP. El comando ping adicionalmente proporciona información sobre el rendimiento de la red, el tiempo de ida y vuelta, y el tiempo para enviar un paquete de solicitud ICMP y recibir un paquete de respuesta ICMP._

#### Tarea 5: ¿Cuál es el nombre de la herramienta más común para encontrar puertos abiertos en un objetivo?

**Nmap**

_Nmap (Network Mapper) es una herramienta gratuita y de código abierto para descubrimiento de redes y auditoría de seguridad. Se utiliza para descubrir hosts y servicios en una red de computadoras enviando paquetes y analizando las respuestas._

#### Tarea 6: ¿Qué servicio identificamos en el puerto 23/tcp durante nuestros escaneos?

**Telnet**

_TELNET (TErminaL NETwork) es un protocolo que permite a una computadora conectarse a otra computadora de forma remota. Sigue el protocolo de red TCP/IP para crear sesiones remotas. Telnet no es un protocolo seguro y es no cifrado._

#### Tarea 7: ¿Qué nombre de usuario puede iniciar sesión en el objetivo a través de telnet con una contraseña en blanco?

**root**

_Si root no funciona, intenta admin o administrator ya que son los más comunes._

#### Tarea 8: Envía la flag de root

b40abdfe23665f766f9c61ecba8a4c19

<figure><img src="../../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>
