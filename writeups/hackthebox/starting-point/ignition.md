# Ignition

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Common Applications (Aplicaciones Comunes):** Son programas o herramientas utilizadas ampliamente en la industria de la informática, como servidores web, bases de datos o aplicaciones de correo electrónico. Aunque son populares por su facilidad de uso y soporte, si no se configuran adecuadamente o se mantienen actualizadas, pueden ser puntos vulnerables para los atacantes.
  * **Magento:** Plataforma de comercio electrónico de código abierto utilizada para crear tiendas en línea. Si no se asegura correctamente, Magento puede ser objetivo de ataques como inyecciones SQL, explotación de vulnerabilidades conocidas o el uso de credenciales débiles, lo que pone en riesgo la seguridad de la tienda y los datos de los clientes.
  * **Reconnaissance (Reconocimiento):** Fase crucial en el proceso de pruebas de penetración, donde se recopila información detallada sobre el objetivo. Este proceso incluye el escaneo de puertos, el análisis de los servicios disponibles, la identificación de vulnerabilidades y la recopilación de datos sobre las configuraciones del sistema. Permite a los atacantes descubrir posibles vectores de entrada.
  * **Web Site Structure Discovery (Descubrimiento de Estructura del Sitio Web):** Técnica utilizada para mapear la estructura de un sitio web, identificando directorios, archivos ocultos y configuraciones de seguridad, generalmente mediante herramientas de fuerza bruta como `gobuster` o `dirb`. Este descubrimiento puede revelar puntos de acceso no documentados o información sensible.
  * **Weak Credentials (Credenciales Débiles):** Usuario y contraseña fáciles de adivinar, o configuraciones predeterminadas que no se han cambiado. Las credenciales débiles permiten a los atacantes ganar acceso no autorizado a sistemas o servicios, lo que puede ser aprovechado para explotar vulnerabilidades o tomar control de un sistema.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.69.90
```

<figure><img src="../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.69.90
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (4).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **80** perteneciente al servicio **HTTP** está abierto. A continuación, se indagará más sobre estos servicios. Ponemos el dominio en el `/etc/hosts`.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 174332.png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```
gobuster dir -u http://ignition.htb -w /usr/share/dirb/wordlists/common.txt -x php,html
```



Revisamos el directorio `/admin`.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con credenciales típicas como son **admin**:**qwerty123**.

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 175138.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Task 1:** ¿Cuál es la versión del servicio que se está ejecutando en el puerto 80?

* **nginx 1.14.2**

**Task 2:** ¿Cuál es el código de estado HTTP de 3 dígitos que se devuelve al visitar http://{IP de la máquina}/?

* **302**

**Task 3:** ¿Cuál es el nombre del host virtual que la página web espera que se acceda?

* **ignition.htb**

**Task 4:** ¿Cuál es la ruta completa al archivo en una computadora Linux que mantiene una lista local de pares de nombres de dominio a direcciones IP?

* **/etc/hosts**

**Task 5:** Usa una herramienta para hacer fuerza bruta a los directorios en el servidor web. ¿Cuál es la URL completa de la página de inicio de sesión de Magento?

* [**http://ignition.htb/admin**](http://ignition.htb/admin)

**Task 6:** Consulta los requisitos de contraseña para Magento y también intenta buscar las contraseñas más comunes de 2023. ¿Qué contraseña proporciona acceso a la cuenta de administrador?

* **qwerty123**
