# Host Discovery

Cuando realizamos una prueba de penetración interna en una red corporativa, el primer paso es obtener una visión general de los sistemas activos. Para descubrir estos sistemas en la red, podemos utilizar varias opciones de Nmap enfocadas en el descubrimiento de hosts. A continuación, exploraremos diferentes métodos para identificar qué sistemas están en línea.

### Almacenamiento de Resultados

Siempre es recomendable almacenar los resultados de cada escaneo. Esto permite comparar, documentar y generar informes más precisos. Diferentes herramientas pueden ofrecer resultados distintos, por lo que distinguir qué herramienta produjo qué resultados puede ser beneficioso.

### Escanear un Rango de Red

Para descubrir hosts en un rango de red específico, se puede usar el siguiente comando:

```bash
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
```

#### Opciones de Escaneo

* `10.129.2.0/24`: Especifica el rango de red objetivo.
* `-sn`: Desactiva el escaneo de puertos, realizando solo el descubrimiento de hosts.
* `-oA tnet`: Almacena los resultados en todos los formatos que comienzan con el nombre `tnet`.

**Salida Esperada:**

```
10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

Este método de escaneo depende de que los firewalls de los hosts permitan el tráfico ICMP. Si no es así, se deben considerar otras técnicas de escaneo, que se analizarán en "Firewall and IDS Evasion".

### Escanear una Lista de IP

Si se proporciona una lista de IPs durante una prueba de penetración, Nmap puede trabajar con esta lista para realizar el escaneo. Aquí hay un ejemplo de cómo usar una lista de IPs:

1.  **Archivo de Lista de IPs (`hosts.lst`):**

    ```
    10.129.2.4
    10.129.2.10
    10.129.2.11
    10.129.2.18
    10.129.2.19
    10.129.2.20
    10.129.2.28
    ```
2.  **Comando de Escaneo:**

    ```bash
    sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
    ```

#### Opciones de Escaneo

* `-sn`: Desactiva el escaneo de puertos.
* `-oA tnet`: Almacena los resultados en todos los formatos que comienzan con el nombre `tnet`.
* `-iL hosts.lst`: Lee los hosts de la lista `hosts.lst` proporcionada.

**Salida Esperada:**

```
10.129.2.18
10.129.2.19
10.129.2.20
```

En este ejemplo, se detectan solo 3 de los 7 hosts como activos. Esto podría ser debido a configuraciones de firewall que bloquean las solicitudes ICMP.

### Escanear Múltiples IPs

También puedes escanear una pequeña parte de una red especificando múltiples direcciones IP o rangos de IP:

1.  **Escanear Direcciones IP Específicas:**

    ```bash
    sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20 | grep for | cut -d" " -f5
    ```
2.  **Escanear un Rango de IPs:**

    ```bash
    sudo nmap -sn -oA tnet 10.129.2.18-20 | grep for | cut -d" " -f5
    ```

**Salida Esperada:**

```
10.129.2.18
10.129.2.19
10.129.2.20
```

### Escanear una Sola IP

Para verificar si un solo host está activo, utiliza el siguiente comando:

```bash
sudo nmap 10.129.2.18 -sn -oA host
```

#### Opciones de Escaneo

* `10.129.2.18`: Especifica el objetivo del escaneo.
* `-sn`: Desactiva el escaneo de puertos, realizando solo el descubrimiento de hosts.
* `-oA host`: Almacena los resultados en todos los formatos que comienzan con el nombre `host`.

**Salida Esperada:**

```less
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-14 23:59 CEST
Nmap scan report for 10.129.2.18
Host is up (0.087s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```

### Opciones Avanzadas

Para verificar cómo Nmap determina que un host está activo y para usar ICMP Echo Requests, puedes agregar las siguientes opciones:

1.  **Comando de Escaneo con Rastreo de Paquetes:**

    ```bash
    sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace
    ```

    * `-PE`: Realiza el escaneo de ping utilizando solicitudes de eco ICMP.
    * `--packet-trace`: Muestra todos los paquetes enviados y recibidos.
2.  **Comando de Escaneo con Razón:**

    ```bash
    sudo nmap 10.129.2.18 -sn -oA host -PE --reason
    ```

    * `--reason`: Muestra el motivo por el cual un host se considera activo o inactivo.

**Salida Esperada con `--packet-trace`:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```

Este comando desactiva las solicitudes ARP y realiza el escaneo utilizando solo solicitudes de eco ICMP.
