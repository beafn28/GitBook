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
ping -c 1 10.129.228.195
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 102912.png" alt=""><figcaption></figcaption></figure>
