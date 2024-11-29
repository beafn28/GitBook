# Dancing

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **SMB**: Se enfoca en el servicio utilizado.
  * **Protocolos**: Relacionado con los protocolos involucrados.
  * **Reconocimiento**: Proceso de exploración y escaneo.
  * **Anonymous**: Conseguir el acceso.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.1.12
```

<figure><img src="../../../.gitbook/assets/image (530).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Windows** ya que su **ttl=127**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap 10.129.1.12
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (531).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **135** perteneciente al servicio **MSRPC** está abierto, junto con los puertos **139** y **445**, que están asociados a **NetBIOS-SSN** y **Microsoft-DS** respectivamente. Por lo tanto, se procederá a indagar más sobre estos servicios para evaluar posibles vulnerabilidades.

<figure><img src="../../../.gitbook/assets/image (532).png" alt=""><figcaption></figcaption></figure>

Nos hemos conectado al servicio **SMB** y no ha requerido ninguna contraseña.

### 5. 🔑 **Captura de la Flag**

Tras estar dentro listamos y encontramos dos directorios en lo que accederemos por si encontramos la `flag.txt` y nos lo descargamos.

<figure><img src="../../../.gitbook/assets/image (533).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (534).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### Tarea 1: ¿Qué significa el acrónimo VM?

**Server Message Block**\
&#xNAN;_&#x45;l acrónimo SMB corresponde a "Server Message Block", un protocolo de red utilizado para compartir archivos, impresoras y otros recursos entre nodos en una red._

#### Tarea 2: ¿En qué puerto opera SMB?

**445**\
&#xNAN;_&#x45;l puerto 445 es utilizado por el protocolo SMB para la comunicación directa sobre TCP/IP sin necesidad de NetBIOS._

#### Tarea 3: ¿Cuál es el nombre del servicio en el puerto 445 que apareció en nuestro escaneo de Nmap?

**microsoft-ds**\
&#xNAN;_&#x45;l servicio en el puerto 445 que Nmap identifica comúnmente es "microsoft-ds", utilizado por SMB._

#### Tarea 4: ¿Cuál es el 'flag' o 'switch' que podemos usar con la utilidad smbclient para 'listar' los recursos compartidos en Dancing?

**-L**\
&#xNAN;_&#x45;l comando `smbclient -L` se utiliza para listar los recursos compartidos disponibles en un servidor SMB._

#### Tarea 5: ¿Cuántos recursos compartidos hay en Dancing?

**4**\
&#xNAN;_&#x48;ay un total de 4 recursos compartidos en el servidor SMB llamado "Dancing"._

#### Tarea 6: ¿Cuál es el nombre del recurso compartido al que podemos acceder al final con una contraseña en blanco?

**WorkShares**\
&#xNAN;_&#x45;l recurso compartido al que se puede acceder con una contraseña en blanco se llama "WorkShares"._

#### Tarea 7: ¿Cuál es el comando que podemos usar dentro del shell de SMB para descargar los archivos que encontramos?

**get**\
&#xNAN;_&#x45;l comando `get` se usa dentro del shell de SMB para descargar archivos desde el servidor a la máquina local._

**Tarea 8: Envía la flag de root**\
5f61c10dffbc77a704d76016a22f1664

<figure><img src="../../../.gitbook/assets/image (535).png" alt=""><figcaption></figcaption></figure>
