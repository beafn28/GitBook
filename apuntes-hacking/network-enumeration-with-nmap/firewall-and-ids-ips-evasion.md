# Firewall and IDS/IPS Evasion

## Firewalls

Un **firewall** es una medida de seguridad contra intentos de conexión no autorizados desde redes externas. Cada sistema de seguridad de firewall se basa en un componente de software que monitorea el tráfico de red entre el firewall y las conexiones de datos entrantes, y decide cómo manejar la conexión según las reglas establecidas. Revisa si los paquetes de red individuales son permitidos, ignorados o bloqueados. Este mecanismo está diseñado para prevenir conexiones no deseadas que podrían ser potencialmente peligrosas.

## IDS/IPS

Al igual que el firewall, el **sistema de detección de intrusiones (IDS)** y el **sistema de prevención de intrusiones (IPS)** también son componentes basados en software. El IDS escanea la red en busca de posibles ataques, los analiza y reporta cualquier ataque detectado. El IPS complementa al IDS al tomar medidas defensivas específicas si se detecta un posible ataque. El análisis de tales ataques se basa en la coincidencia de patrones y firmas. Si se detectan patrones específicos, como un escaneo de detección de servicios, el IPS puede prevenir los intentos de conexión pendientes.

### Determinación de Firewalls y Sus Reglas

Sabemos que cuando un puerto se muestra como "filtrado", esto puede deberse a varias razones. En la mayoría de los casos, los firewalls tienen ciertas reglas establecidas para manejar conexiones específicas. Los paquetes pueden ser descartados o rechazados. Los paquetes descartados son ignorados, y no se devuelve ninguna respuesta desde el host.

Esto es diferente para los paquetes rechazados, que se devuelven con una bandera RST. Estos paquetes contienen diferentes tipos de códigos de error ICMP o no contienen nada en absoluto.

Algunos de estos errores pueden ser:

* **Net Unreachable**
* **Net Prohibited**
* **Host Unreachable**
* **Host Prohibited**
* **Port Unreachable**
* **Proto Unreachable**

El escaneo TCP ACK de Nmap (`-sA`) es mucho más difícil de filtrar para firewalls y sistemas IDS/IPS que los escaneos SYN (`-sS`) o Connect (`-sT`) porque solo envía un paquete TCP con solo la bandera ACK. Cuando un puerto está cerrado o abierto, el host debe responder con una bandera RST. A diferencia de las conexiones salientes, todos los intentos de conexión (con la bandera SYN) desde redes externas suelen ser bloqueados por los firewalls. Sin embargo, los paquetes con la bandera ACK a menudo son permitidos por el firewall porque el firewall no puede determinar si la conexión fue establecida primero desde la red externa o desde la red interna.

### **SYN-Scan**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace
```

**Salida:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:56 CEST
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:22 S ttl=53 id=22412 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:25 S ttl=50 id=62291 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:21 S ttl=58 id=38696 iplen=44  seq=4092255222 win=1024 <mss 1460>
RCVD (0.0329s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=40884 iplen=72 ]
RCVD (0.0341s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
RCVD (1.0386s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
SENT (1.1366s) TCP 10.10.14.2:57348 > 10.129.2.28:25 S ttl=44 id=6796 iplen=44  seq=4092320759 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.0053s latency).

PORT   STATE    SERVICE
21/tcp filtered ftp
22/tcp open     ssh
25/tcp filtered smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

### **ACK-Scan**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace
```

**Salida:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:57 CEST
SENT (0.0422s) TCP 10.10.14.2:49343 > 10.129.2.28:21 A ttl=49 id=12381 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:22 A ttl=41 id=5146 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:25 A ttl=49 id=5800 iplen=40  seq=0 win=1024
RCVD (0.1252s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=55628 iplen=68 ]
RCVD (0.1268s) TCP 10.129.2.28:22 > 10.10.14.2:49343 R ttl=64 id=0 iplen=40  seq=1660784500 win=0
SENT (1.3837s) TCP 10.10.14.2:49344 > 10.129.2.28:25 A ttl=59 id=21915 iplen=40  seq=0 win=1024
Nmap scan report for 10.129.2.28
Host is up (0.083s latency).

PORT   STATE      SERVICE
21/tcp filtered   ftp
22/tcp unfiltered ssh
25/tcp filtered   smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

#### Opciones de Escaneo

* `10.129.2.28`: Escanea el objetivo especificado.
* `-p 21,22,25`: Escanea solo los puertos especificados.
* `-sS`: Realiza un escaneo SYN en los puertos especificados.
* `-sA`: Realiza un escaneo ACK en los puertos especificados.
* `-Pn`: Desactiva las solicitudes de eco ICMP.
* `-n`: Desactiva la resolución DNS.
* `--disable-arp-ping`: Desactiva el ping ARP.
* `--packet-trace`: Muestra todos los paquetes enviados y recibidos.

Observa los paquetes RCVD y sus banderas en los resultados del escaneo. Con el escaneo SYN (`-sS`), nuestro objetivo intenta establecer la conexión TCP enviando un paquete de vuelta con las banderas SYN-ACK (SA) establecidas. Con el escaneo ACK (`-sA`), obtenemos la bandera RST porque el puerto TCP 22 está abierto. Para el puerto TCP 25, no recibimos ningún paquete de vuelta, lo que indica que los paquetes son descartados.

## Detección de IDS/IPS

A diferencia de los firewalls y sus reglas, la detección de sistemas IDS/IPS es mucho más difícil porque estos son sistemas de monitoreo de tráfico pasivos. Los sistemas **IDS** examinan todas las conexiones entre hosts. Si el IDS encuentra paquetes que contienen los contenidos o especificaciones definidas, el administrador es notificado y toma las medidas adecuadas en el peor de los casos.

Los sistemas **IPS** toman medidas configuradas por el administrador de forma independiente para prevenir ataques potenciales automáticamente. Es esencial saber que IDS y IPS son aplicaciones diferentes y que IPS complementa al IDS.

Durante una prueba de penetración, se recomienda usar varios servidores privados virtuales (VPS) con diferentes direcciones IP para determinar si estos sistemas están presentes en la red objetivo. Si el administrador detecta un posible ataque en la red objetivo, el primer paso suele ser bloquear la dirección IP desde la cual proviene el ataque. Como resultado, ya no podremos acceder a la red usando esa IP, y se contactará a nuestro Proveedor de Servicios de Internet (ISP) para bloquear todo acceso a Internet.

Los sistemas IDS por sí solos suelen estar ahí para ayudar a los administradores a detectar ataques potenciales en su red. Luego, pueden decidir cómo manejar tales conexiones. Podemos activar ciertas medidas de seguridad desde un administrador, por ejemplo, al escanear agresivamente un solo puerto y su servicio. Basado en si se toman medidas de seguridad específicas, podemos detectar si la red tiene algunas aplicaciones de monitoreo.

Un método para determinar si hay un sistema IPS en la red objetivo es escanear desde un solo host (VPS). Si en algún momento este host es bloqueado y no tiene acceso a la red objetivo, sabemos que el administrador ha tomado algunas medidas de seguridad. En consecuencia, podemos continuar nuestra prueba de penetración con otro VPS.

Por lo tanto, sabemos que debemos ser más discretos con nuestros escaneos y, en el mejor de los casos, disfrazar todas las interacciones con la red objetivo y sus servicios.

## Decoy

En algunos casos, los administradores bloquean subredes específicas de diferentes regiones en principio, lo que previene cualquier acceso a la red objetivo. Otro ejemplo es cuando el IPS debería bloquearnos. Por esta razón, el método de escaneo **Decoy** (`-D`) es la opción correcta. Con este método, Nmap genera varias direcciones IP aleatorias insertadas en el encabezado IP para disfrazar el origen del paquete enviado. Podemos generar aleatoriamente un número específico (por ejemplo, 5) de direcciones IP separadas por dos puntos (:). Nuestra IP real se coloca aleatoriamente entre las direcciones IP generadas. En el siguiente ejemplo, nuestra IP real está colocada en la segunda posición. Otro punto crítico es que los decoys deben estar activos. De lo contrario, el servicio en el objetivo puede ser inalcanzable debido a los mecanismos de seguridad de SYN-flooding.

### **Escaneo Usando Decoys**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

#### **Salida:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 16:14 CEST
SENT (0.0378s) TCP 102.52.161.59:59289 > 10.129.2.28:80 S ttl=42 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0378s) TCP 10.10.14.2:59289 > 10.129.2.28:80 S ttl=59 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 210.120.38.29:59289 > 10.129.2.28:80 S ttl=37 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 191.6.64.171:59289 > 10.129.2.28:80 S ttl=38 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 184.178.194.209:59289 > 10.129.2.28:80 S ttl=39 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 43.21.121.33:59289 > 10.129.2.28:80 S ttl=55 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
RCVD (0.1370s) TCP 10.129.2.28:80 > 10.10.14.2:59289 SA ttl=64 id=0 iplen=44  seq=4056111701 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.099s latency).

PORT   STATE SERVICE
80/tcp open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

#### Opciones de Escaneo

* `10.129.2.28`: Escanea el objetivo especificado.
* `-p 80`: Escanea solo el puerto especificado.
* `-sS`: Realiza un escaneo SYN en los puertos especificados.
* `-Pn`: Desactiva las solicitudes de eco ICMP.
* `-n`: Desactiva la resolución DNS.
* `--disable-arp-ping`: Desactiva el ping ARP.
* `--packet-trace`: Muestra todos los paquetes enviados y recibidos.
* `-D RND:5`: Genera cinco direcciones IP aleatorias que indican la IP de origen desde la cual proviene la conexión.

Los paquetes falsificados a menudo son filtrados por los ISP y routers, incluso si provienen del mismo rango de red. Por lo tanto, también podemos especificar las direcciones IP de nuestros servidores VPS y usarlas en combinación con la manipulación del "ID IP" en los encabezados IP para escanear el objetivo.

Otro escenario sería que solo subredes individuales no tengan acceso a los servicios específicos del servidor. Por lo tanto, también podemos especificar manualmente la dirección IP de origen (`-S`) para probar si obtenemos mejores resultados con esta. Los decoys pueden usarse para escaneos SYN, ACK, ICMP y detección de sistemas operativos. Veamos un ejemplo y determinemos cuál es el sistema operativo más probable.

### **Prueba de Regla de Firewall**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -n -Pn -p445 -O
```

**Salida:**

```less
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:23 CEST
Nmap scan report for 10.129.2.28
Host is up (0.032s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3.14 seconds
```

### **Escaneo Usando Diferente IP de Origen**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0
```

#### **Salida:**

```less
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:16 CEST
Nmap scan report for 10.129.2.28
Host is up (0.010s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Linux 2.6.32 - 2.6.35 (94%), Linux 2.6.32 - 3.5 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.11 seconds
```

#### **Opciones de Escaneo**

* `10.129.2.28`: Escanea el objetivo especificado.
* `-n`: Desactiva la resolución DNS.
* `-Pn`: Desactiva las solicitudes de eco ICMP.
* `-p 445`: Escanea solo el puerto especificado.
* `-O`: Realiza una detección de sistema operativo.
* `-S`: Escanea el objetivo usando una IP de origen diferente.
* `10.129.2.200`: Especifica la dirección IP de origen.
* `-e tun0`: Envía todas las solicitudes a través de la interfaz especificada.

## Proxy DNS

Por defecto, Nmap realiza una resolución inversa de DNS a menos que se indique lo contrario, para encontrar información más relevante sobre nuestro objetivo. Estas consultas DNS suelen ser aceptadas, ya que se espera que el servidor web se encuentre y se visite. Las consultas DNS se realizan a través del puerto UDP 53. El puerto TCP 53 se utilizaba anteriormente solo para los llamados "Transferencias de zona" entre servidores DNS o para la transferencia de datos mayores a 512 bytes. Cada vez más, esto está cambiando debido a las expansiones de IPv6 y DNSSEC. Estos cambios están haciendo que muchas solicitudes DNS se realicen a través del puerto TCP 53.

Sin embargo, Nmap aún nos permite especificar nuestros propios servidores DNS usando `--dns-server <ns>,<ns>`. Este método puede ser fundamental si estamos en una zona desmilitarizada (DMZ). Los servidores DNS de la empresa suelen ser más confiables que los de Internet. Por ejemplo, podríamos usarlos para interactuar con los hosts de la red interna. Como otro ejemplo, podemos usar el puerto TCP 53 como puerto de origen (`--source-port`) para nuestros escaneos. Si el administrador usa el firewall para controlar este puerto y no filtra correctamente IDS/IPS, nuestros paquetes TCP serán confiables y pasarán.

### Escaneo SYN de un Puerto Filtrado

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace
```

#### **Salida:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 22:50 CEST
SENT (0.0417s) TCP 10.10.14.2:33436 > 10.129.2.28:50000 S ttl=41 id=21939 iplen=44  seq=736533153 win=1024 <mss 1460>
SENT (1.0481s) TCP 10.10.14.2:33437 > 10.129.2.28:50000 S ttl=46 id=6446 iplen=44  seq=736598688 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT      STATE    SERVICE
50000/tcp filtered ibm-db2

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

### Escaneo SYN Desde el Puerto DNS

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53
```

**Salida:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 22:50 CEST
SENT (0.0482s) TCP 10.10.14.2:53 > 10.129.2.28:50000 S ttl=58 id=27470 iplen=44  seq=4003923435 win=1024 <mss 1460>
RCVD (0.0608s) TCP 10.129.2.28:50000 > 10.10.14.2:53 SA ttl=64 id=0 iplen=44  seq=540635485 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).

PORT      STATE SERVICE
50000/tcp open  ibm-db2
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

**Opciones de Escaneo:**

* `10.129.2.28`: Escanea el objetivo especificado.
* `-p 50000`: Escanea solo el puerto especificado.
* `-sS`: Realiza un escaneo SYN en los puertos especificados.
* `-Pn`: Desactiva las solicitudes de eco ICMP.
* `-n`: Desactiva la resolución DNS.
* `--disable-arp-ping`: Desactiva el ping ARP.
* `--packet-trace`: Muestra todos los paquetes enviados y recibidos.
* `--source-port 53`: Realiza los escaneos desde el puerto de origen especificado.

Ahora que hemos descubierto que el firewall acepta el puerto TCP 53, es muy probable que los filtros IDS/IPS también estén configurados de manera más débil que otros. Podemos probar esto intentando conectarnos a este puerto usando Netcat.

### Conectar al Puerto Filtrado

```bash
sherlock28@htb[/htb]$ ncat -nv --source-port 53 10.129.2.28 50000
```

#### **Salida:**

```vbnet
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.129.2.28:50000.
220 ProFTPd
```

## Laboratorios de Evasión de Firewall e IDS/IPS

En las siguientes tres secciones, se presentan diferentes escenarios para practicar donde debemos escanear nuestro objetivo. Las reglas del firewall y los sistemas IDS/IPS protegen los sistemas, por lo que necesitamos usar las técnicas mostradas para eludir las reglas del firewall y hacerlo de la manera más discreta posible. De lo contrario, seremos bloqueados por el IPS.
