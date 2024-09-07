# Host and Port Scanning

## Estados de los Puertos

Al realizar un escaneo de puertos, Nmap puede identificar seis estados diferentes para cada puerto escaneado:

| Estado               | Descripción                                                                                                                                                                   |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **open**             | El puerto está abierto y acepta conexiones. Esto puede incluir conexiones TCP, datagramas UDP y asociaciones SCTP.                                                            |
| **closed**           | El puerto está cerrado. Nmap recibe un paquete con la bandera RST. Este estado también se usa para verificar si el objetivo está activo.                                      |
| **filtered**         | Nmap no puede determinar si el puerto está abierto o cerrado porque no se recibe respuesta del destino o se recibe un código de error.                                        |
| **unfiltered**       | El puerto es accesible, pero no se puede determinar si está abierto o cerrado. Este estado solo ocurre durante el escaneo TCP-ACK.                                            |
| **open\|filtered**   | Si no obtenemos una respuesta para un puerto específico, `Nmap`lo configuraremos en ese estado. Esto indica que un firewall o un filtro de paquetes puede proteger el puerto. |
| **closed\|filtered** | Este estado solo ocurre en los escaneos **inactivos de ID de IP** e indica que fue imposible determinar si el puerto escaneado está cerrado o filtrado por un firewall.       |

### Descubrimiento de Puertos TCP Abiertos

Por defecto, Nmap escanea los 1000 puertos TCP más comunes utilizando el escaneo SYN (`-sS`). Este método requiere permisos de root para crear paquetes TCP sin procesar. Sin permisos de root, se realiza un escaneo TCP (`-sT`). Los puertos se pueden especificar uno por uno (`-p 22,25,80,139,445`), por rango (`-p 22-445`), por puertos principales (`--top-ports=10`) o escanear todos los puertos (`-p-`). También se puede realizar un escaneo rápido de los 100 puertos principales (`-F`).

#### **Escaneo de los 10 Puertos TCP Principales**

```bash
sudo nmap 10.129.2.28 --top-ports=10
```

**Resultado:**

```bash
PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
```

En este ejemplo, Nmap escaneó los 10 puertos TCP principales y mostró su estado.

**Seguimiento de Paquetes**

```bash
sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping
```

**Resultado:**

```bash
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44 seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40 seq=0 win=0
```

En este escaneo, se muestra el seguimiento de paquetes enviados y recibidos, revelando el estado del puerto.

### Escaneo de Conexión TCP

El escaneo de conexión TCP (`-sT`) utiliza el protocolo de enlace de tres vías para determinar si un puerto está abierto o cerrado. Si el puerto responde con un paquete SYN-ACK, está abierto. Si responde con un paquete RST, está cerrado.

**Escaneo de Conexión en el Puerto TCP 443**

```bash
sudo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT
```

**Resultado:**

```arduino
PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack
```

#### Puertos Filtrados

Un puerto puede estar filtrado debido a un firewall que descarta o rechaza paquetes. Cuando los paquetes son descartados, Nmap no recibe respuesta y marca el puerto como `filtered`. Si los paquetes son rechazados, Nmap recibe una respuesta ICMP que indica que el puerto no es accesible.

### **Puertos Filtrados**

```bash
sudo nmap 10.129.2.28 -p 139 --packet-trace -n --disable-arp-ping -Pn
```

**Resultado:**

```bash
PORT    STATE    SERVICE
139/tcp filtered netbios-ssn
```

**Ejemplo: Rechazo de Paquetes**

```bash
sudo nmap 10.129.2.28 -p 445 --packet-trace -n --disable-arp-ping -Pn
```

**Resultado:**

```bash
PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
```

En este caso, recibimos un mensaje ICMP indicando que el puerto es inalcanzable.

### Descubrimiento de Puertos UDP Abiertos

El escaneo UDP (`-sU`) es más lento que el escaneo TCP debido a la naturaleza sin estado de UDP. No siempre se reciben respuestas para paquetes UDP, por lo que Nmap puede marcar puertos como `open|filtered`.

**Escaneo UDP**

```bash
sudo nmap 10.129.2.28 -F -sU
```

**Resultado:**

```arduino
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
```

**Escaneo UDP Detallado**

```bash
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason
```

**Resultado:**

```arduino
PORT    STATE SERVICE    REASON
137/udp open  netbios-ns udp-response ttl 64
```

### Escaneo de Versiones

El escaneo de versiones (`-sV`) se utiliza para obtener información adicional sobre los servicios que se ejecutan en los puertos abiertos, como versiones y nombres de servicios.

**Escaneo de Versiones en el Puerto TCP 445**

```bash
sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason -sV
```

**Resultado:**

```arduino
PORT    STATE SERVICE     REASON         VERSION
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
```

Para más detalles sobre técnicas de escaneo de puertos, puedes consultar [la documentación de Nmap](https://nmap.org/book/man-port-scanning-techniques.html)
