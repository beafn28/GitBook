# Preignition

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Custom Applications (Aplicaciones Personalizadas):** Software desarrollado específicamente para satisfacer necesidades únicas de una organización o usuario, en lugar de utilizar soluciones genéricas. Pueden presentar vulnerabilidades de seguridad si no se implementan correctamente.
  * **Apache:** Servidor web de código abierto ampliamente utilizado para alojar sitios y aplicaciones web. Su seguridad depende de una configuración adecuada, actualizaciones constantes y protección contra ataques como Directory Traversal o ejecución remota de código.
  * **Reconnaissance (Reconocimiento):** Proceso de recopilación de información sobre un objetivo para identificar posibles vulnerabilidades. Incluye técnicas como escaneo de puertos, análisis de DNS y enumeración de subdominios.
  * **Web Site Structure Discovery (Descubrimiento de Estructura del Sitio Web):** Técnica utilizada en pruebas de seguridad para mapear la estructura de un sitio web, identificando directorios, archivos ocultos y configuraciones sensibles mediante herramientas como gobuster o dirb.
  * **Default Credentials (Credenciales Predeterminadas):** Usuario y contraseña estándar que vienen configurados por defecto en sistemas y aplicaciones. Su uso sin cambios representa un alto riesgo de seguridad, ya que pueden ser explotados fácilmente por atacantes.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.100.238 
```

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sC -Pn 10.129.100.238 
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Si solo se muestra el puerto **80** abierto con el servicio **HTTP**. En este caso, el enfoque debe centrarse en la enumeración del servicio web alojado en el servidor **nginx**.

<figure><img src="../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Realizamos un escaneo de directorios.

```bash
gobuster dir -w /usr/share/wordlists/dirb/common.txt -u 10.129.100.238
```

<figure><img src="../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Miramos el contenido.

<figure><img src="../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Ponemos las credenciales por defecto **admin**:**admin**.

<figure><img src="../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Tarea 1**\
&#xNAN;**¿El brute-forcing de directorios es una técnica utilizada para verificar muchas rutas en un servidor web y encontrar páginas ocultas. ¿Cuál es otro nombre para esto?**\
dir busting&#x20;

**Tarea 2**\
&#xNAN;**¿Qué switch usamos en el escaneo de nmap para especificar que queremos realizar una detección de versión?**\
-sV&#x20;

**Tarea 3**\
&#xNAN;**¿Qué servicio informa Nmap que está ejecutándose en el puerto 80/tcp?**\
http&#x20;

**Tarea 4**\
&#xNAN;**¿Qué nombre de servidor y versión del servicio está ejecutándose en el puerto 80/tcp?**\
nginx 1.14.2&#x20;

**Tarea 5**\
&#xNAN;**¿Qué switch usamos en Gobuster para especificar que queremos realizar dir busting específicamente?**\
dir&#x20;

**Tarea 6**\
**Cuando usamos Gobuster para dir busting, ¿qué switch agregamos para asegurarnos de que encuentre páginas PHP?**\
-x php&#x20;

**Tarea 7**\
&#xNAN;**¿Qué página se encuentra durante nuestras actividades de dir busting?**\
admin.php&#x20;

**Tarea 8**\
&#xNAN;**¿Cuál es el código de estado HTTP reportado por Gobuster para la página descubierta?**\
200&#x20;
