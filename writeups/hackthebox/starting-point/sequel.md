# Sequel

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Vulnerability Assessment**: Evaluación de la seguridad y detección de vulnerabilidades en bases de datos y sistemas.
  * **Databases**: Análisis de riesgos y brechas de seguridad en bases de datos MySQL.
  * **MySQL**: Revisión y auditoría de configuraciones de seguridad en servidores MySQL.
  * **SQL**: Identificación de vulnerabilidades y explotación de inyecciones SQL en aplicaciones web.
  * **Reconnaissance**: Recolección de información y mapeo de sistemas para identificar posibles puntos de ataque.
  * **Weak Credentials**: Detección de credenciales débiles o predeterminadas que comprometen la seguridad del sistema.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.241.245
```

<figure><img src="../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.241.245
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **3306** perteneciendo al servicio **MySQL** está abierto, por lo que se procederá a indagar más.

```bash
mysql -h 10.129.241.245 -u root
```

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

```bash
show databases;
use htb;
show tables;
select * from config; 
```

### 6. ❓Preguntas

#### **Tarea 1:** Durante nuestro escaneo, ¿ qué puerto encontramos sirviendo MySQL?

**3306**\
_El puerto 3306 es el puerto estándar utilizado por MySQL para comunicaciones con bases de datos._

#### **Tarea 2:** ¿Qué versión de MySQL desarrollada por la comunidad está ejecutándose en el objetivo?

**MariaDB**\
_La versión de MySQL que se está utilizando es MariaDB, una bifurcación desarrollada por la comunidad._

#### **Tarea 3:** Al usar el cliente de línea de comandos de MySQL, ¿qué opción necesitamos usar para especificar un nombre de usuario para iniciar sesión?

**-u**\
_La opción -u se utiliza para especificar el nombre de usuario en el cliente de línea de comandos de MySQL._

#### **Tarea 4:** ¿Qué nombre de usuario nos permite iniciar sesión en esta instancia de MariaDB sin proporcionar una contraseña?

**root**\
_El nombre de usuario root permite iniciar sesión sin contraseña en muchas configuraciones predeterminadas de MariaDB._

#### **Tarea 5:** En SQL, ¿ qué símbolo se usa para especificar dentro de la consulta que queremos mostrar todo lo que hay en una tabla?

**\***\
_El símbolo \* se utiliza en SQL para seleccionar todos los campos de una tabla._

#### **Tarea 6:** En SQL, ¿qué símbolo necesitamos para terminar cada consulta?

**;**\
_El símbolo ; se usa para finalizar cada consulta en SQL._

#### **Tarea 7:** Hay tres bases de datos en esta instancia de MySQL que son comunes a todas las instancias de MySQL. ¿Cuál es el nombre de la cuarta que es única para este host?

**htb**\
_La cuarta base de datos única para este host se llama htb._

#### Tarea 8: Enviar Flag

7b4bec00d1a39e3dd4e021ec3d915da8

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>
