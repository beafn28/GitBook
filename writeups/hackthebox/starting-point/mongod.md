# Mongod

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **MongoDB**: Base de datos NoSQL de código abierto que almacena datos en formato JSON (BSON). Es popular por su flexibilidad y escalabilidad, pero requiere configuraciones adecuadas para prevenir vulnerabilidades como la exposición de puertos sin protección o la falta de autenticación.
  * **Databases (Bases de Datos)**: Sistemas de almacenamiento de datos organizados que permiten la gestión y recuperación eficiente de información. Las bases de datos pueden ser relacionales (SQL) o no relacionales (NoSQL), y una configuración incorrecta o sin medidas de seguridad adecuadas puede poner en riesgo la integridad y confidencialidad de los datos.
  * **Reconnaissance (Reconocimiento)**: Proceso de recolección de información sobre un objetivo para identificar posibles puntos de ataque. Esto incluye el análisis de servicios activos, puertos abiertos, sistemas operativos, y más. Es una fase clave en las pruebas de penetración.
  * **Misconfiguration (Mala Configuración)**: Error en la configuración de un sistema, red o servicio que crea vulnerabilidades de seguridad. Ejemplos incluyen configuraciones incorrectas de firewalls, permisos excesivos, o puertos abiertos innecesarios, que pueden ser explotados por atacantes.
  * **Anonymous/Guest Access (Acceso Anónimo/Acceso de Invitado)**: Acceso permitido a un sistema o recurso sin la necesidad de autenticarse. Si no se configura correctamente, este tipo de acceso puede permitir que usuarios no autorizados exploren o modifiquen datos de un sistema, aumentando el riesgo de explotación.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.163.203
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (4).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sV -T4 -vv -p- 10.129.163.203
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 154557.png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **27017** correspondiente a **MongoDB** están abiertos. A continuación, se indagará más sobre estos servicios.

Descargamos MongoDB.

```bash
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.7.tgz
tar xvf mongodb-linux-x86_64-3.4.7.tgz
```

Ejecutamos dentro del directorio.

```bash
./mongo mongodb://10.129.163.203:27017
```

Estando dentro vemos la información.

```
show dbs
use sensitive_information;
show collections;
db.flag.find().pretty();
```

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Task 1**

¿Cuántos puertos TCP están abiertos en la máquina?\
**Respuesta:** `2`

**Task 2**

¿Qué servicio se está ejecutando en el puerto 27017 del host remoto?\
**Respuesta:** `MongoDB 3.6.8`

**Task 3**

¿Qué tipo de base de datos es MongoDB? (Elige: SQL o NoSQL)\
**Respuesta:** `NoSQL`

**Task 4**

¿Cuál es el nombre del comando para la shell de MongoDB que se instala con el paquete `mongodb-clients`?\
**Respuesta:** `Mongosh`

**Task 5**

¿Qué comando se usa para listar todas las bases de datos presentes en el servidor de MongoDB?\
**Respuesta:** `show dbs`

**Task 6**

¿Qué comando se usa para listar las colecciones dentro de una base de datos?\
**Respuesta:** `show collections`

**Task 7**

¿Qué comando se usa para volcar el contenido de todos los documentos dentro de la colección llamada `flag` en un formato fácil de leer?\
**Respuesta:** `db.flag.find().pretty()`
