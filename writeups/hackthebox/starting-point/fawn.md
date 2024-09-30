# Fawn

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **FTP**: Se enfoca en el servicio utilizado.
  * **Protocolos**: Relacionado con los protocolos involucrados.
  * **Reconocimiento**: Proceso de exploración y escaneo.
  * **Anonymous**: Conseguir el acceso.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.12.83
```

<figure><img src="../../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap 10.129.12.83
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **21** perteneciendo al servicio **FTP** está abierto, por lo que se procederá a indagar más.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-30 111907.png" alt=""><figcaption></figcaption></figure>

Nos hemos conectado al servicio **FTP** utilizando el usuario **anonymous**, que no requiere una contraseña.

### 5. 🔑 **Captura de la Flag**

Tras estar dentro listamos y encontramos un archivo llamado`flag.txt` que deberá mostrar la flag que nos preguntan por eso nos lo descargamos.

<figure><img src="../../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: ¿Qué significa el acrónimo VM?

**File Transfer Protocol**

_El acrónimo FTP corresponde a "File Transfer Protocol", que es un protocolo estándar de la red utilizado para la transferencia de archivos entre sistemas a través de la red._

#### **Tarea 2:** ¿En qué puerto escucha el servicio FTP por lo general?

**21**

_El puerto 21 es el puerto estándar para el servicio FTP. Es el puerto predeterminado donde los servidores FTP escuchan las solicitudes de conexión._

#### **Tarea 3:** FTP envía datos en claro, sin cifrado. ¿Qué acrónimo se usa para un protocolo posterior diseñado para proporcionar una funcionalidad similar a FTP pero de forma segura, como una extensión del protocolo SSH?

**SFTP**

_SFTP, o "Secure File Transfer Protocol", es un protocolo que proporciona una forma segura de transferir archivos, en contraste con FTP, que no cifra los datos en tránsito._

#### **Tarea 4:** ¿Cuál es el comando que podemos usar para enviar una solicitud de eco ICMP y probar nuestra conexión con el objetivo?

**ping**

_El comando `ping` se utiliza para enviar paquetes ICMP Echo Request a una dirección IP y recibir una respuesta para verificar la conectividad de red y medir la latencia._

#### **Tarea 5:** En tus escaneos, ¿ qué versión de FTP está corriendo en el objetivo?

**vsftpd 3.0.3**

_La versión "vsftpd 3.0.3" indica que el objetivo está utilizando vsftpd, que es una implementación segura del servidor FTP._

#### **Tarea 6:** En tus escaneos, ¿ qué tipo de sistema operativo está ejecutando el objetivo?

**UNIX**

_UNIX es un sistema operativo multiusuario y multiproceso._

#### **Tarea 7:** ¿Cuál es el comando que necesitamos ejecutar para mostrar el menú de ayuda del cliente 'ftp'?

**ftp -h**

_El comando `ftp -h` se utiliza para mostrar la ayuda del cliente FTP, proporcionando información sobre los comandos y su uso._

#### **Tarea 8:** ¿Cuál es el nombre de usuario que se usa a través de FTP cuando quieres iniciar sesión sin tener una cuenta?

**anonymous**

_El usuario `anonymous` se utiliza comúnmente en servidores FTP públicos que permiten acceso anónimo para la descarga de archivos sin necesidad de una cuenta._

#### **Tarea 9:** ¿Cuál es el código de respuesta que obtenemos para el mensaje FTP 'Login successful'?

**230**

_El código de respuesta `230` en FTP indica que el inicio de sesión ha sido exitoso y que el usuario ha sido autenticado correctamente._

#### **Tarea 10:** Hay un par de comandos que podemos usar para listar los archivos y directorios disponibles en el servidor FTP. Uno es `dir`. ¿Cuál es el otro que es una forma común de listar archivos en un sistema Linux?

**ls**

_El comando `ls` se utiliza en sistemas Linux para listar los archivos y directorios en un directorio, y es equivalente a `dir` en FTP._

#### **Tarea 11:** ¿Cuál es el comando usado para descargar el archivo que encontramos en el servidor FTP?

**get**

_El comando `get` se utiliza en FTP para descargar un archivo desde el servidor al cliente._

#### Tarea 12: Envía la flag de root

035db21c881520061c53e0536e44f815

<figure><img src="../../../.gitbook/assets/image (529).png" alt=""><figcaption></figcaption></figure>

