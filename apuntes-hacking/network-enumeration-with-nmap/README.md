# Network Enumeration with NMAP

## Introducción a Nmap

**Nmap** (Network Mapper) es una herramienta de auditoría de seguridad y análisis de redes de código abierto, escrita en C, C++, Python y Lua. Está diseñada para escanear redes e identificar qué hosts están disponibles, los servicios y aplicaciones en ejecución, incluyendo sus nombres y versiones cuando es posible. Además, Nmap puede identificar sistemas operativos y versiones de los hosts, y evaluar configuraciones de filtros de paquetes, firewalls y sistemas de detección de intrusos (IDS).

### Casos de Uso

Nmap es ampliamente utilizado por administradores de red y especialistas en seguridad informática para:

* Auditar la seguridad de redes
* Realizar pruebas de penetración
* Verificar configuraciones de firewall e IDS
* Mapear redes
* Analizar respuestas
* Identificar puertos abiertos
* Evaluar vulnerabilidades

### Arquitectura de Nmap

Nmap ofrece varias técnicas de escaneo que proporcionan distintos tipos de información sobre los objetivos. Estas técnicas se pueden clasificar en las siguientes categorías:

1. **Descubrimiento de Host**: Determina qué hosts están disponibles en la red.
2. **Escaneo de Puertos**: Identifica puertos abiertos en los hosts.
3. **Enumeración y Detección de Servicios**: Determina los servicios y aplicaciones que se están ejecutando, junto con sus versiones.
4. **Detección del Sistema Operativo**: Identifica el sistema operativo y su versión en el host.
5. **Interacción Programable con el Servicio de Destino**: Utiliza el motor de scripts de Nmap para ejecutar scripts personalizados.

### Sintaxis

La sintaxis básica de Nmap es la siguiente:

```bash
nmap <scan types> <options> <target>
```

### Técnicas de Escaneo

Nmap ofrece diversas técnicas de escaneo que pueden utilizar diferentes tipos de conexiones y paquetes para obtener distintos resultados. A continuación se muestran algunas de las técnicas de escaneo disponibles:

```bash
nmap --help
```

#### Técnicas de Escaneo Comunes

* `-sS`: TCP SYN scan (escaneo SYN)
* `-sT`: TCP Connect scan (escaneo Connect)
* `-sU`: UDP scan (escaneo UDP)
* `-sN`: TCP Null scan (escaneo Null)
* `-sF`: TCP FIN scan (escaneo FIN)
* `-sX`: TCP Xmas scan (escaneo Xmas)
* `-sO`: IP protocol scan (escaneo de protocolo IP)
* `-sI <zombie host[:probeport]>`: Idle scan (escaneo Idle)
* `-b <FTP relay host>`: FTP bounce scan (escaneo FTP bounce)

#### Ejemplo de Escaneo TCP-SYN

El escaneo TCP-SYN (`-sS`) es uno de los métodos de escaneo más populares y predeterminados en Nmap. Este método permite escanear varios miles de puertos por segundo sin completar el protocolo de enlace de tres vías TCP.

* **Funcionamiento**:
  * Envia un paquete con el indicador SYN.
  * Si el objetivo responde con un paquete SYN-ACK, el puerto se considera **abierto**.
  * Si responde con un paquete RST, el puerto se considera **cerrado**.
  * Si no se recibe respuesta, el puerto se considera **filtrado**.

**Ejemplo de uso**:

```bash
sudo nmap -sS localhost
```

**Salida**:

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-11 22:50 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000010s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5432/tcp open  postgresql
5901/tcp open  vnc-1

Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
```

En este ejemplo, se muestran cuatro puertos TCP abiertos: 22 (ssh), 80 (http), 5432 (postgresql) y 5901 (vnc-1).
