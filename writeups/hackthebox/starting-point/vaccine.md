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
ping -c 1 10.129.245.165
```



Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.
