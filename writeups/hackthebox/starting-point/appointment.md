# Appointment

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Databases**: Evaluación de la seguridad en la gestión de bases de datos Apache y MariaDB.
  * **Apache**: Análisis de configuraciones y vulnerabilidades en servidores Apache.
  * **MariaDB**: Revisión de la seguridad y rendimiento en instancias de MariaDB.
  * **PHP**: Auditoría de seguridad en aplicaciones PHP conectadas a bases de datos.
  * **SQL**: Pruebas de integridad y prevención de inyecciones SQL en consultas.
  * **Reconnaissance**: Escaneo y mapeo de servicios y bases de datos accesibles en la red.
  * **SQL Injection**: Identificación y explotación de vulnerabilidades de inyección SQL en aplicaciones web.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.189.92
```

<figure><img src="../../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.189.92
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **80** perteneciendo al servicio **HTTP** está abierto, por lo que se procederá a indagar más.

<figure><img src="../../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

Es un formulario de **Login** probamos a hacer una **SQL Injection** para que nos deje loguear ya que metemos cualquier valor se envía pero no nos dice nada.

### 5. 🔑 **Captura de la Flag**

Para evitar la autenticación de un login mediante inyección SQL, se utilizan técnicas que manipulan la consulta SQL empleada por la aplicación para verificar las credenciales del usuario. Las inyecciones más comunes introducen condiciones que siempre resultan verdaderas, permitiendo el acceso sin necesidad de un usuario y contraseña válidos.

Aquí tienes algunos ejemplos típicos:

1. **' OR '1'='1'--**: Este string inyecta una condición siempre verdadera (`'1'='1'`), lo que permite eludir el chequeo de autenticación.
2. **' OR 'a'='a'--**: Similar al anterior, asegura que la condición sea siempre verdadera.
3. **admin'--**: Si la aplicación no maneja correctamente los comentarios en SQL, este string puede truncar la consulta después de "admin", evitando la verificación de la contraseña.

Estos códigos se suelen ingresar en los campos de nombre de usuario o contraseña de un formulario de login. Si la aplicación es vulnerable y no sanitiza adecuadamente la entrada del usuario, el SQL inyectado puede engañar al sistema para que permita el acceso sin credenciales válidas.

Para más detalles y cómo defenderse de estos ataques, puedes consultar la página de [HackTricks sobre SQL login bypass](https://github.com/HackTricks-wiki/hacktricks/blob/master/pentesting-web/login-bypass/sql-login-bypass.md)​

<figure><img src="../../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### **Tarea 1:** ¿Qué significa el acrónimo SQL?

**Structured Query Language**\
_Structured Query Language es el significado de SQL, que se utiliza para gestionar y manipular bases de datos relacionales._

#### **Tarea 2:** ¿Cuál es uno de los tipos más comunes de vulnerabilidades SQL?

**SQL injection**\
_SQL injection es una de las vulnerabilidades más comunes en aplicaciones que usan bases de datos SQL._

#### **Tarea 3:** ¿Cuál es la clasificación en el OWASP Top 10 de 2021 para esta vulnerabilidad?

**A03:2021-Injection**\
_La inyección de código, como SQL injection, se clasifica bajo A03:2021-Injection en el OWASP Top 10 de 2021._

#### **Tarea 4:** ¿Qué servicio y versión reporta Nmap que están corriendo en el puerto 80 del objetivo?

**Apache httpd 2.4.38 ((Debian))**\
_Nmap indica que el servicio Apache httpd 2.4.38 (Debian) está ejecutándose en el puerto 80._

#### **Tarea 5:** ¿Cuál es el puerto estándar utilizado para el protocolo HTTPS?

**443**\
_El puerto 443 es el puerto estándar para el protocolo HTTPS._

#### **Tarea 6:** ¿Cómo se llama una carpeta en la terminología de aplicaciones web?

**Directory**\
_En las aplicaciones web, una carpeta se denomina directory._

#### Tarea 7: ¿Qué código de respuesta HTTP se da para errores de 'No Encontrado'?

**404**\
_El código 404 es la respuesta HTTP estándar para un error de "Not Found"._

#### **Tarea 8:** Gobuster es una herramienta utilizada para fuerza bruta de directorios en un servidor web. ¿Qué switch usamos con Gobuster para especificar que buscamos directorios y no subdominios?

**dir**\
_El switch dir se usa con Gobuster para buscar directorios._

#### **Tarea 9:** ¿Qué carácter único se puede usar para comentar el resto de una línea en MySQL?

**#**\
_El carácter # se usa para comentar una línea en MySQL._

#### **Tarea 10:** Si la entrada del usuario no se maneja con cuidado, podría interpretarse como un comentario. Usa un comentario para iniciar sesión como administrador sin conocer la contraseña. ¿Cuál es la primera palabra en la página web devuelta?

**Congratulations**\
_La primera palabra en la página devuelta después de un login exitoso como administrador es Congratulations._

#### Tarea 11: Enviar Flag

e3d0796d002a446c0e622226f42e9672

<figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>
