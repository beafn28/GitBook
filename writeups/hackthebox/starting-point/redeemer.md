# Redeemer

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Redis**: Evaluación de vulnerabilidades en la base de datos en memoria Redis.
  * **Protocolos**: Análisis de seguridad en el protocolo TCP utilizado por Redis en el puerto 6379.
  * **Reconocimiento**: Escaneo y descubrimiento de instancias Redis expuestas en la red.
  * **Anonymous/Guest Access**: Verificación de acceso sin autenticación en instancias Redis.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.200.227
```

<figure><img src="../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.200.227
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **6379** perteneciente al servicio **Redis** está abierto, lo que indica que una instancia de Redis (versión 5.0.7) está en ejecución. Además, los puertos **44386** y **49455** aparecen como filtrados, lo que sugiere que podrían estar protegidos por un firewall o reglas de filtrado de tráfico. Por lo tanto, se procederá a indagar más sobre el servicio Redis en el puerto 6379 para evaluar posibles vulnerabilidades y configuraciones incorrectas que podrían ser explotadas.

<figure><img src="../../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

Redis permite crear múltiples bases de datos dentro de una sola instancia. Para seleccionar una base de datos específica, utilizamos el comando `select` seguido del índice de la base de datos.

Para contar el número de claves presentes en una base de datos en particular, ejecutamos el comando `keys *` después de seleccionar la base de datos deseada. En este caso, seleccionamos la base de datos con índice 0 y ejecutamos el comando para obtener el número de claves.

<figure><img src="../../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Como podemos observar anteriormente sale una flag por lo que ejecutamos.

```bash
get flag
```

<figure><img src="../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: ¿Qué puerto TCP está abierto en la máquina?

**6379**\
_El puerto TCP **6379** está abierto en la máquina, que es comúnmente utilizado por Redis._

#### Tarea 2: ¿Qué servicio se está ejecutando en el puerto que está abierto en la máquina?

**Redis**\
_El servicio que se está ejecutando en el puerto abierto **6379** es **Redis**, un almacén de valores clave._

#### Tarea 3: ¿Qué tipo de base de datos es Redis? Elige entre las siguientes opciones: (i) Base de Datos en Memoria, (ii) Base de Datos Tradicional

**Base de Datos en Memoria**\
_Redis es una **Base de Datos en Memoria**, lo que significa que almacena datos en la memoria del sistema para un acceso rápido._

#### Tarea 4: ¿Qué utilidad de línea de comandos se utiliza para interactuar con el servidor Redis? Escribe el nombre del programa que introducirías en la terminal sin argumentos.

**redis-cli**\
_La utilidad de línea de comandos **redis-cli** se utiliza para interactuar con el servidor Redis._

#### Tarea 5: ¿Qué flag se usa con la utilidad de línea de comandos de Redis para especificar el nombre del host?

**-h**\
_La flag **-h** se usa con `redis-cli` para especificar el nombre del host del servidor Redis._

#### Tarea 6: Una vez conectado a un servidor Redis, ¿qué comando se utiliza para obtener la información y estadísticas sobre el servidor Redis?

**info**\
_El comando **info** se usa en Redis para obtener información y estadísticas sobre el servidor._

#### Tarea 7: ¿Cuál es la versión del servidor Redis que se está utilizando en la máquina objetivo?

**5.0.7**\
_El servidor Redis en la máquina objetivo está utilizando la versión **5.0.7**._

#### Tarea 8: ¿Qué comando se utiliza para seleccionar la base de datos deseada en Redis?

**select**\
_El comando **select** se usa para elegir una base de datos específica dentro de Redis._

#### Tarea 9: ¿Cuántas claves hay en la base de datos con índice 0?

**4**\
_Hay 4 claves presente en la base de datos con índice 0._

#### Tarea 10: ¿Qué comando se utiliza para obtener todas las claves en una base de datos?

**keys \***\
_El comando **keys \*** se utiliza para recuperar todas las claves en una base de datos específica de Redis._

#### Tarea 11: Enviar Flag

03e1d2b376c37ab3f5319922053953eb

<figure><img src="../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>
