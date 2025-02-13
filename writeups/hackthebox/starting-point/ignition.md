# Ignition

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Common Applications (Aplicaciones Comunes):** Son programas o herramientas utilizadas ampliamente en la industria de la informática, como servidores web, bases de datos o aplicaciones de correo electrónico. Aunque son populares por su facilidad de uso y soporte, si no se configuran adecuadamente o se mantienen actualizadas, pueden ser puntos vulnerables para los atacantes.
  * **Magento:** Plataforma de comercio electrónico de código abierto utilizada para crear tiendas en línea. Si no se asegura correctamente, Magento puede ser objetivo de ataques como inyecciones SQL, explotación de vulnerabilidades conocidas o el uso de credenciales débiles, lo que pone en riesgo la seguridad de la tienda y los datos de los clientes.
  * **Reconnaissance (Reconocimiento):** Fase crucial en el proceso de pruebas de penetración, donde se recopila información detallada sobre el objetivo. Este proceso incluye el escaneo de puertos, el análisis de los servicios disponibles, la identificación de vulnerabilidades y la recopilación de datos sobre las configuraciones del sistema. Permite a los atacantes descubrir posibles vectores de entrada.
  * **Web Site Structure Discovery (Descubrimiento de Estructura del Sitio Web):** Técnica utilizada para mapear la estructura de un sitio web, identificando directorios, archivos ocultos y configuraciones de seguridad, generalmente mediante herramientas de fuerza bruta como `gobuster` o `dirb`. Este descubrimiento puede revelar puntos de acceso no documentados o información sensible.
  * **Weak Credentials (Credenciales Débiles):** Usuario y contraseña fáciles de adivinar, o configuraciones predeterminadas que no se han cambiado. Las credenciales débiles permiten a los atacantes ganar acceso no autorizado a sistemas o servicios, lo que puede ser aprovechado para explotar vulnerabilidades o tomar control de un sistema.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.1.27
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 165216.png" alt=""><figcaption></figcaption></figure>
