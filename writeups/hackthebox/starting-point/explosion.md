# Explosion

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
*   **Tags:**&#x20;

    **Reconnaissance**: Proceso de recolección de información sobre un objetivo para identificar posibles puntos de ataque, mediante técnicas como el escaneo de puertos o el análisis de redes.

    **Programming (Programación):** La programación es el proceso de diseñar, escribir y mantener código fuente para desarrollar software, scripts o aplicaciones.\
    **RDP (Remote Desktop Protocol):** Protocolo que permite la conexión remota a un sistema, utilizado comúnmente para administración, pero vulnerable a ataques como fuerza bruta o explotación de configuraciones inseguras.\
    **Weak Credentials:** Credenciales débiles en el sistema.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.150.176
```

<figure><img src="../../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Windows** ya que su **ttl=127**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sC -Pn 10.129.150.176
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (8).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **135** perteneciente al servicio **MSRPC**, el puerto **139** correspondiente a **NetBIOS-SSN**, el puerto **445** relacionado con **Microsoft-DS**, el puerto **3389** perteneciente a **MS-WBT-Server (RDP)** y el puerto **5985** correspondiente a **WSMan** están abiertos. A continuación, se indagará más sobre estos servicios.

<pre><code><strong>xfreerdp /v:10.129.150.176 /cert-ignore /u:Administrator
</strong></code></pre>

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se nos abre una instancia de un **Windows**. Abrimos el CMD y somos administradores.

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 104320.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 104423.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Tarea 1**\
¿Para qué significa el acrónimo de tres letras RDP?\
**Remote Desktop Protocol**&#x20;

**Tarea 2**\
¿Cuál es un acrónimo de tres letras que se refiere a la interacción con el host a través de una interfaz de línea de comandos?\
**CLI**&#x20;

**Tarea 3**\
¿Qué hay de las interacciones con la interfaz gráfica de usuario?\
**GUI**&#x20;

**Tarea 4**\
¿Cuál es el nombre de una antigua herramienta de acceso remoto que venía sin cifrado por defecto y escucha en el puerto TCP 23?\
**Telnet**

**Tarea 5**\
¿Cuál es el nombre del servicio que se ejecuta en el puerto 3389 TCP?\
**Telnet**

**Tarea 6**\
¿Cuál es el switch utilizado para especificar la dirección IP del host de destino cuando se usa xfreerdp?\
&#xNAN;**/v:**&#x20;

**Tarea 7**\
¿Qué nombre de usuario devuelve exitosamente una proyección de escritorio con una contraseña en blanco?\
**Administrator**&#x20;
