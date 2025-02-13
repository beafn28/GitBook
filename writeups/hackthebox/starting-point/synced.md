# Synced

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Rsync:** Protocolo y herramienta utilizada para la transferencia eficiente de archivos entre sistemas, comúnmente empleada para copias de seguridad y sincronización de datos. Si no se configura adecuadamente, puede exponer archivos sensibles o permitir accesos no autorizados a través de conexiones sin autenticación.
  * **Protocols (Protocolos):** Conjunto de reglas y estándares que regulan la comunicación entre dispositivos en una red. Algunos protocolos como HTTP, SSH y SMB pueden ser explotados si no están configurados con medidas de seguridad adecuadas, como cifrado o autenticación robusta.
  * **Reconnaissance (Reconocimiento):** Fase inicial en pruebas de seguridad donde se recopila información sobre un sistema objetivo. Esto puede incluir escaneo de puertos, enumeración de servicios y análisis de vulnerabilidades, lo que permite a los atacantes identificar posibles puntos de entrada.
  * **Anonymous/Guest Access (Acceso Anónimo/Acceso de Invitado):** Configuración que permite el acceso a un sistema sin necesidad de autenticación. Puede ser útil en ciertos entornos, pero si no se gestiona correctamente, puede facilitar la filtración de información o el acceso no autorizado a recursos críticos.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.61.108
```

<figure><img src="../../../.gitbook/assets/image (1181).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.61.108
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (1182).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **873** perteneciente al servicio **Rsync** está abierto. A continuación, se indagará más sobre estos servicios.

```bash
rsync rsync://10.129.61.108
rsync rsync://10.129.61.108/public
rsync rsync://10.129.61.108/public/flag ./
```

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (1183).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1184).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Task 1**

¿Cuál es el puerto predeterminado para `rsync`?\
**Respuesta:** `873`

**Task 2**

¿Cuántos puertos TCP están abiertos en el host remoto?\
**Respuesta:** `1`

**Task 3**

¿Cuál es la versión del protocolo utilizada por `rsync` en la máquina remota?\
**Respuesta:** `31`

**Task 4**

¿Cuál es el nombre del comando más común en Linux para interactuar con `rsync`?\
**Respuesta:** `rsync`

**Task 5**

¿Qué credenciales debes proporcionar a `rsync` para usar la autenticación anónima? (Opciones: `anonymous:anonymous`, `anonymous`, `None`, `rsync:rsync`)\
**Respuesta:** `none`

**Task 6**

¿Cuál es la opción para **listar únicamente** los recursos compartidos y archivos en `rsync`? (No incluir los caracteres `--` al inicio)\
**Respuesta:** `list-only`
