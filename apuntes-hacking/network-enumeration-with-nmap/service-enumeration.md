# Service Enumeration

## Detección de la versión del servicio

Es fundamental determinar con precisión la aplicación y su versión para buscar vulnerabilidades conocidas y analizar el código fuente de esa versión si está disponible. Un número de versión exacto permite buscar exploits específicos que se adapten al servicio y al sistema operativo del objetivo.

### **Escaneo de puertos con Nmap**

Es recomendable realizar un escaneo rápido de puertos inicialmente para obtener una visión general de los puertos disponibles, generando menos tráfico y reduciendo el riesgo de ser detectados por mecanismos de seguridad. Paralelamente, se puede ejecutar un escaneo completo de puertos abiertos (`-p-`) y utilizar el escaneo de versiones (`-sV`) para detectar servicios específicos y sus versiones.

Para monitorear el progreso del escaneo, se puede presionar la barra espaciadora, lo que muestra el estado en tiempo real.

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV
```

**Opciones de escaneo**

| Opción             | Descripción                                                   |
| ------------------ | ------------------------------------------------------------- |
| `10.129.2.28`      | IP del objetivo.                                              |
| `-p-`              | Escanea todos los puertos.                                    |
| `-sV`              | Detecta la versión del servicio en los puertos especificados. |
| `--stats-every=5s` | Muestra el progreso del escaneo cada 5 segundos.              |
| `-v`               | Incrementa el nivel de detalle mostrado durante el escaneo.   |

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV --stats-every=5s
```

#### **Ejemplo con mayor verbosidad:**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -v
```

## Acaparamiento de banners

Una vez finalizado el escaneo, Nmap muestra los puertos abiertos, los servicios asociados y sus versiones activas en el sistema.

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV
```

#### **Resultado del escaneo:**

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:00 CEST
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).
Not shown: 65525 closed ports
PORT      STATE    SERVICE      VERSION
22/tcp    open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp    open     smtp         Postfix smtpd
80/tcp    open     http         Apache httpd 2.4.29 ((Ubuntu))
110/tcp   open     pop3         Dovecot pop3d
143/tcp   open     imap         Dovecot imapd (Ubuntu)
993/tcp   open     ssl/imap     Dovecot imapd (Ubuntu)
995/tcp   open     ssl/pop3     Dovecot pop3d
```

#### **Opciones de escaneo adicionales:**

| Opción               | Descripción                                      |
| -------------------- | ------------------------------------------------ |
| `-Pn`                | Desactiva las solicitudes de eco ICMP (ping).    |
| `-n`                 | Desactiva la resolución de DNS.                  |
| `--disable-arp-ping` | Desactiva el ping ARP.                           |
| `--packet-trace`     | Muestra todos los paquetes enviados y recibidos. |

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace
```

#### Análisis más detallado de banners

Después de un apretón de manos TCP de tres vías, algunos servidores pueden enviar un banner de identificación. Este banner puede proporcionar información útil, como la distribución del sistema operativo. Sin embargo, Nmap puede no mostrar este tipo de información detallada. A continuación, un ejemplo de cómo conectarse manualmente a un servicio SMTP para capturar este banner.

**Captura de banner con `nc`:**

```bash
sherlock28@htb[/htb]$ nc -nv 10.129.2.28 25
```

**Respuesta:**

```bash
220 inlane ESMTP Postfix (Ubuntu)
```

#### Captura del tráfico con `tcpdump`

Es posible capturar e inspeccionar el tráfico con `tcpdump` para ver el intercambio de paquetes en el apretón de manos TCP de tres vías:

```bash
sherlock28@htb[/htb]$ sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28
```

**Tráfico interceptado:**

```bash
18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], seq 1798872233, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 331260178 ecr 0,sackOK,eol], length 0
18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], seq 1130574379, ack 1798872234, win 65160, options [mss 1460,sackOK,TS val 1800383922 ecr 331260178,nop,wscale 7], length 0
18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], seq 1:36, ack 1, win 510, options [nop,nop,TS val 1800383985 ecr 331260304], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)
```

**Intercambio TCP en el apretón de manos de tres vías:**

1. **SYN**: Cliente inicia conexión.
2. **SYN-ACK**: Servidor responde.
3. **ACK**: Cliente confirma la conexión.
4. **PSH-ACK**: Servidor envía datos (banner).
5. **ACK**: Cliente confirma la recepción de los datos.
