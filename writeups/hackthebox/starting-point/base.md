# Base

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Custom Applications (Aplicaciones Personalizadas):** Software desarrollado específicamente para cumplir con requerimientos particulares de una empresa u organización. Estas aplicaciones pueden contener vulnerabilidades no descubiertas debido a la falta de auditorías de seguridad y pueden ser explotadas mediante ingeniería inversa o análisis de código.
  * **Source Code Analysis (Análisis de Código Fuente):** Revisión manual o automatizada del código fuente de una aplicación para identificar vulnerabilidades como inyecciones SQL, ejecución remota de código, errores de autenticación, entre otros. Es una técnica clave en pruebas de seguridad de aplicaciones.
  * **Authentication (Autenticación):** Proceso mediante el cual un sistema verifica la identidad de un usuario antes de conceder acceso. Si está mal implementado, puede ser susceptible a ataques como fuerza bruta, autenticación rota o bypass de autenticación.
  * **Apache:** Servidor web ampliamente utilizado en internet. Si no se configura adecuadamente o no se actualizan sus módulos, puede ser vulnerable a ataques como **Directory Traversal, ejecución de código remoto o fuga de información**.
  * **PHP:** Lenguaje de programación usado en desarrollo web. Errores comunes como **inyecciones SQL, ejecución remota de código (RCE) y PHP Type Juggling** pueden permitir a los atacantes comprometer un servidor.
  * **Reconnaissance (Reconocimiento):** Fase en la que se recopila información sobre un objetivo mediante escaneo de puertos, análisis de servicios, revisión de archivos accesibles y técnicas de fingerprinting para descubrir posibles vulnerabilidades.
  * **Web Site Structure Discovery (Descubrimiento de Estructura del Sitio Web):** Técnica utilizada para identificar directorios y archivos ocultos dentro de un sitio web. Herramientas como **gobuster, dirb o wfuzz** se emplean para revelar rutas sensibles que podrían contener credenciales, configuraciones o puntos de entrada ocultos.&#x20;
  * **SUDO Exploitation (Explotación de SUDO):** Abuso de permisos elevados otorgados por SUDO en sistemas Linux para ejecutar comandos como root. Configuraciones incorrectas pueden permitir a usuarios sin privilegios escalar privilegios a administrador.
  * **Authentication Bypass (Bypass de Autenticación):** Métodos para eludir los mecanismos de autenticación mediante técnicas como **inyección SQL, manipulación de cookies, fuerza bruta o uso de credenciales por defecto**.
  * **Clear Text Credentials (Credenciales en Texto Claro):** Exposición de contraseñas sin cifrar en archivos de configuración, logs o bases de datos. Este error facilita el acceso no autorizado y compromete la seguridad de la aplicación o sistema.
  * **Arbitrary File Upload (Carga Arbitraria de Archivos):** Vulnerabilidad en la que un atacante puede cargar archivos maliciosos en un servidor, como **web shells en PHP**, lo que permite ejecución remota de comandos y toma de control del sistema.
  * **Information Disclosure (Divulgación de Información):** Fuga involuntaria de información sensible, como credenciales, rutas de archivos, versiones de software o claves API, que puede ser utilizada por atacantes para facilitar el acceso o explotación de vulnerabilidades.
  * **PHP Type Juggling (Manipulación de Tipos en PHP):** Técnica de explotación en PHP que aprovecha la conversión automática de tipos de datos en comparaciones de valores. Puede ser utilizada para **bypassear autenticación, alterar valores en bases de datos o evadir restricciones de seguridad**.
  * **Vulnerability Assessment (Evaluación de Vulnerabilidades):** Proceso de identificación, clasificación y análisis de vulnerabilidades en un sistema, aplicación o red. Se realiza utilizando herramientas automatizadas y análisis manual para detectar fallos de seguridad que puedan ser explotados por atacantes.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.238.219
```

<figure><img src="../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.238.219
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.&#x20;

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-14 200328.png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** correspondiente a **HTTP** están abiertos. A continuación, se indagará más sobre estos servicios.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-14 200842.png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```
gobuster dir -u http://10.129.238.219 -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos llama atención el directorio `/login`.

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Mostramos el contenido de **login.php.swp**.

```bash
strings login.php.swp | tac
```

Después de analizar el código fuente en PHP, se observa que las credenciales ingresadas por el usuario se comparan con las variables almacenadas en `config.php` para verificar si coinciden.

¡Espera un momento! En el código se utiliza `strcmp`, y en este caso, su uso es inseguro. Esto da lugar a una vulnerabilidad de tipo _juggling_.

Subimos una Reverse Shell.&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha.

```
http://10.129.238.219/_uploaded/
```

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el contenido del archivo **config.php**.

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisando el archivo `/etc/passwd` encontramos un usuario llamado **john**.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con ese usuario y la contraseña que descubrimos anteriormente.

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Viendo los permisos SUDO.

```bash
sudo -l
```

{% embed url="https://gtfobins.github.io/gtfobins/find/" %}

```
sudo /usr/bin/find /bin -exec /bin/bash \;
```

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-14 213808.png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### **Tarea 1**

**¿Qué dos puertos TCP están abiertos en el host remoto?**\
22,80

#### **Tarea 2**

**¿Cuál es la ruta relativa en el servidor web para la página de inicio de sesión?**\
/login/login.php

#### **Tarea 3**

**¿Cuántos archivos hay en el directorio '/login'?**\
3

#### **Tarea 4**

**¿Cuál es la extensión de archivo de un archivo de intercambio (swap)?**\
.swp

#### **Tarea 5**

**¿Qué función de PHP se utiliza en el código backend para comparar el nombre de usuario y la contraseña enviados por el usuario con los valores válidos?**\
strcmp()

#### **Tarea 6**

**¿En qué directorio se almacenan los archivos subidos?**\
/\_uploaded

#### **Tarea 7**

**¿Qué usuario existe en el host remoto con un directorio home?**\
john

#### **Tarea 8**

**¿Cuál es la contraseña del usuario presente en el sistema?**\
thisisagoodpassword

#### **Tarea 9**

**¿Cuál es la ruta completa del comando que el usuario john puede ejecutar como root en el host remoto?**\
/usr/bin/find

#### **Tarea 10**

**¿Qué acción puede usar el comando 'find' para ejecutar comandos?**\
exec
