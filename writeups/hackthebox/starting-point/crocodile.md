# Crocodile

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Custom Applications**: Evaluación de seguridad en aplicaciones personalizadas.
  * **Protocols**: Análisis de protocolos de comunicación utilizados por aplicaciones.
  * **Apache**: Revisión de configuración y seguridad en servidores Apache.
  * **FTP**: Verificación de la seguridad en servidores FTP.
  * **Reconnaissance**: Recopilación de información sobre los activos de la red.
  * **Web Site Structure Discovery**: Mapeo y análisis de la estructura del sitio web.
  * **Clear Text Credentials**: Detección de credenciales enviadas en texto claro.
  * **Anonymous/Guest Access**: Comprobación de accesos sin autenticación en servicios expuestos.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.49.118
```

<figure><img src="../../../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.49.118 
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (537).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **21** perteneciendo al servicio **FTP** y el **puerto 80** perteneciendo al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

<figure><img src="../../../.gitbook/assets/image (538).png" alt=""><figcaption></figcaption></figure>

Así de primeras no se nos muestra nada relevante ni en el código fuente por lo que usamos la herramienta **Whatweb** para saber más información.

<figure><img src="../../../.gitbook/assets/image (539).png" alt=""><figcaption></figcaption></figure>

Se nos muestra dos correos por lo que miraremos si son relevantes o no. A continuación me conecto a través del servicio **FTP**.

<figure><img src="../../../.gitbook/assets/image (540).png" alt=""><figcaption></figcaption></figure>

Descubrimos unas credenciales.

<figure><img src="../../../.gitbook/assets/image (541).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://10.129.49.118/ -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 
```

<figure><img src="../../../.gitbook/assets/image (544).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Miramos el `login.php`. Probamos las credenciales descubiertas en el servicio **FTP**. Tenemos suerte con la última credencial.

<figure><img src="../../../.gitbook/assets/image (542).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (543).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### **Tarea 1: ¿Qué opción de escaneo de Nmap utiliza scripts predeterminados durante un escaneo?**

**-sC**

La opción `-sC` en Nmap emplea el uso de scripts predeterminados durante el escaneo. Estos scripts están incluidos en el Nmap Scripting Engine (NSE) y ayudan a realizar tareas comunes como la detección de versiones y la comprobación de vulnerabilidades.

**Tarea 2: ¿Qué versión del servicio se encuentra ejecutándose en el puerto 21?**

**vsftpd 3.0.3**

El puerto 21 se utiliza para el servicio FTP, y en este caso, la versión del servicio es `vsftpd 3.0.3`.

#### **Tarea 3: ¿Qué código FTP se devuelve para el mensaje "Anonymous FTP login allowed"?**

**230**

El código `230` en FTP indica que el inicio de sesión anónimo está permitido y ha sido exitoso.

#### **Tarea 4: Después de conectar al servidor FTP usando el cliente FTP, ¿ qué nombre de usuario proporcionamos cuando se nos solicita iniciar sesión de forma anónima?**

**anonymous**

Para iniciar sesión de forma anónima en un servidor FTP, se usa el nombre de usuario `anonymous`.

#### **Tarea 5: Después de conectar al servidor FTP de forma anónima, ¿qué comando podemos usar para descargar los archivos que encontramos en el servidor FTP?**

**get**

El comando `get` se utiliza en FTP para descargar archivos del servidor al cliente.

**Tarea 6: ¿Cuál es uno de los nombres de usuario con privilegios más altos en 'allowed.userlist' que descargamos del servidor FTP?**

**admin**

En la lista `allowed.userlist`, el nombre de usuario `admin` sugiere un nivel de privilegios alto.

#### **Tarea 7: ¿Qué versión del servidor Apache HTTP está ejecutándose en el host objetivo?**

**Apache httpd 2.4.41**

La versión del servidor Apache HTTP en el host objetivo es `Apache httpd 2.4.41`.

#### **Tarea 8: ¿Qué opción podemos usar con Gobuster para especificar que estamos buscando tipos de archivos específicos?**

**-x**

La opción `-x` en Gobuster permite especificar tipos de archivos a buscar durante el escaneo de directorios.

**Tarea 9: ¿Qué archivo PHP podemos identificar con el escaneo de directorios que nos proporcionará la oportunidad de autenticar en el servicio web?**

**login.php**

El archivo `login.php` es comúnmente utilizado para proporcionar una interfaz de inicio de sesión en aplicaciones web.

#### Tarea 10: Enviar Flag

c7110277ac44d78b6a9fff2232434d16

<figure><img src="../../../.gitbook/assets/image (545).png" alt=""><figcaption></figcaption></figure>
