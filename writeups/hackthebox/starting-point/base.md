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
ping -c 1 10.129.30.230
```

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.28.231
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.&#x20;
