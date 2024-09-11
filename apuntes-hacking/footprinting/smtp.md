# SMTP

El **Protocolo de Transferencia de Correo Simple (SMTP)** es un protocolo para enviar correos electrónicos en una red IP. Puede utilizarse entre un cliente de correo y un servidor de correo saliente o entre dos servidores SMTP. SMTP a menudo se combina con los protocolos IMAP o POP3, que pueden recuperar y enviar correos electrónicos. En principio, es un protocolo basado en cliente-servidor, aunque SMTP puede utilizarse tanto entre un cliente y un servidor como entre dos servidores SMTP. En este caso, un servidor actúa efectivamente como cliente.

Por defecto, los servidores SMTP aceptan solicitudes de conexión en el puerto 25. Sin embargo, los servidores SMTP más nuevos también utilizan otros puertos, como el puerto TCP 587. Este puerto se utiliza para recibir correos de usuarios/servidores autenticados, normalmente usando el comando STARTTLS para cambiar la conexión en texto claro existente a una conexión cifrada. Los datos de autenticación están protegidos y ya no son visibles en texto claro a través de la red. Al comienzo de la conexión, se realiza la autenticación cuando el cliente confirma su identidad con un nombre de usuario y una contraseña. Los correos electrónicos pueden ser transmitidos. Para ello, el cliente envía al servidor las direcciones del remitente y del destinatario, el contenido del correo y otra información y parámetros. Después de que el correo ha sido transmitido, la conexión se termina nuevamente. El servidor de correo comienza entonces a enviar el correo a otro servidor SMTP.

SMTP funciona sin cifrar por defecto y transmite todos los comandos, datos o información de autenticación en texto claro. Para prevenir la lectura no autorizada de datos, SMTP se utiliza junto con el cifrado SSL/TLS. En ciertas circunstancias, un servidor utiliza un puerto diferente al puerto TCP estándar 25 para la conexión cifrada, por ejemplo, el puerto TCP 465.

Una función esencial de un servidor SMTP es prevenir el spam mediante mecanismos de autenticación que permiten solo a usuarios autorizados enviar correos electrónicos. Para ello, la mayoría de los servidores SMTP modernos soportan la extensión del protocolo ESMTP con SMTP-Auth. Después de enviar su correo, el cliente SMTP, también conocido como Agente de Usuario de Correo (MUA), lo convierte en un encabezado y un cuerpo y sube ambos al servidor SMTP. Este tiene un Agente de Transferencia de Correo (MTA), la base del software para enviar y recibir correos electrónicos. El MTA verifica el correo para tamaño y spam y luego lo almacena. Para aliviar al MTA, ocasionalmente se le precede un Agente de Envío de Correo (MSA), que verifica la validez, es decir, el origen del correo electrónico. Este MSA también se llama servidor Relay. Estos son muy importantes más adelante, ya que se puede llevar a cabo el llamado **Ataque de Relay Abierto** en muchos servidores SMTP debido a una configuración incorrecta. Discutiremos este ataque y cómo identificar el punto débil para él un poco más adelante. El MTA luego busca en el DNS la dirección IP del servidor de correo del destinatario.

Al llegar al servidor SMTP de destino, los paquetes de datos se reensamblan para formar un correo electrónico completo. Desde allí, el Agente de Entrega de Correo (MDA) lo transfiere a la bandeja de entrada del destinatario. Cliente (MUA) ➞ Agente de Envío (MSA) ➞ Relay Abierto (MTA) ➞ Agente de Entrega de Correo (MDA) ➞ Bandeja de Entrada (POP3/IMAP)

Pero SMTP tiene dos desventajas inherentes al protocolo de red:

1. El primero es que enviar un correo electrónico usando SMTP no devuelve una confirmación de entrega utilizable. Aunque las especificaciones del protocolo prevén este tipo de notificación, su formato no está especificado por defecto, por lo que generalmente solo se devuelve un mensaje de error en inglés, incluyendo el encabezado del mensaje no entregado.
2. Los usuarios no están autenticados cuando se establece una conexión, y por lo tanto, el remitente de un correo electrónico es poco confiable. Como resultado, los relays SMTP abiertos a menudo se utilizan para enviar spam en masa. Los originadores usan direcciones de remitente falsas arbitrarias para no ser rastreados (suplantación de correo). Hoy en día, se utilizan muchas técnicas de seguridad diferentes para prevenir el mal uso de los servidores SMTP. Por ejemplo, los correos electrónicos sospechosos se rechazan o se mueven a cuarentena (carpeta de spam). Por ejemplo, responsables de esto son el protocolo de identificación **DomainKeys (DKIM)**, el **Sender Policy Framework (SPF)**.

Para ello, se ha desarrollado una extensión para SMTP llamada **SMTP Extendido (ESMTP)**. Cuando se habla de SMTP en general, normalmente se refiere a ESMTP. ESMTP utiliza TLS, que se realiza después del comando EHLO enviando STARTTLS. Esto inicializa la conexión SMTP protegida por SSL, y a partir de este momento, toda la conexión está cifrada y, por lo tanto, es más o menos segura. Ahora, la extensión AUTH PLAIN para la autenticación también puede utilizarse de forma segura.

## Configuración por Defecto

Cada servidor SMTP puede ser configurado de muchas maneras, al igual que todos los demás servicios. Sin embargo, hay diferencias porque el servidor SMTP solo es responsable de enviar y reenviar correos electrónicos.

### Configuración por Defecto de SMTP

```bash
sherlock28@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```

El envío y la comunicación también se realizan mediante comandos especiales que hacen que el servidor SMTP ejecute lo que el usuario requiere.

| Comando        | Descripción                                                                                           |
| -------------- | ----------------------------------------------------------------------------------------------------- |
| **AUTH PLAIN** | AUTH es una extensión del servicio utilizada para autenticar al cliente.                              |
| **HELO**       | El cliente inicia sesión con el nombre de su computadora y así comienza la sesión.                    |
| **MAIL FROM**  | El cliente nombra al remitente del correo electrónico.                                                |
| **RCPT TO**    | El cliente nombra al destinatario del correo electrónico.                                             |
| **DATA**       | El cliente inicia la transmisión del correo electrónico.                                              |
| **RSET**       | El cliente aborta la transmisión iniciada pero mantiene la conexión entre cliente y servidor.         |
| **VRFY**       | El cliente verifica si hay una bandeja de entrada disponible para la transferencia de mensajes.       |
| **EXPN**       | El cliente también verifica si hay una bandeja de entrada disponible para mensajes con este comando.  |
| **NOOP**       | El cliente solicita una respuesta del servidor para evitar la desconexión debido al tiempo de espera. |
| **QUIT**       | El cliente termina la sesión.                                                                         |

Para interactuar con el servidor SMTP, podemos usar la herramienta `telnet` para inicializar una conexión TCP con el servidor SMTP. La inicialización real de la sesión se realiza con el comando mencionado anteriormente, **HELO** o **EHLO**.

### Ejemplo de Telnet - HELO/EHLO

```bash
sherlock28@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb

EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```

El comando **VRFY** puede usarse para enumerar los usuarios existentes en el sistema. Sin embargo, esto no siempre funciona. Dependiendo de cómo esté configurado el servidor SMTP, el servidor SMTP puede emitir el código 252 y confirmar la existencia de un usuario que no existe en el sistema. Una lista de todos los códigos de respuesta SMTP se puede encontrar aquí.

### Ejemplo de Telnet - VRFY

```bash
sherlock28@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root

VRFY cry0l1t3

252 2.0.0 cry0l1t3

VRFY testuser

252 2.0.0 testuser

VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Por lo tanto, nunca se debe confiar completamente en los resultados de herramientas automáticas. Después de todo, ejecutan comandos preconfigurados, pero ninguna de las funciones especifica explícitamente cómo el administrador configura el servidor probado.

A veces, podemos tener que trabajar a través de un proxy web. También podemos hacer que este proxy web se conecte al servidor SMTP. El comando que enviaríamos se vería algo así: `CONNECT 10.129.14.128:25 HTTP/1.0`.

Todos los comandos que ingresamos en la línea de comandos para enviar un correo electrónico son conocidos de cualquier programa de cliente de correo como Thunderbird, Gmail, Outlook y muchos otros. Especificamos el asunto, a quién debe ir el correo electrónico, CC, BCC y la información que queremos compartir con otros. Por supuesto, lo mismo funciona desde la línea de comandos.

Para enviar un correo electrónico utilizando SMTP, se puede usar la herramienta `telnet` para establecer una conexión con el servidor SMTP. A continuación, se muestra un ejemplo de cómo enviar un correo electrónico.

### Ejemplo de Telnet para Enviar un Correo Electrónico

```bash
sherlock28@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server

EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING

MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok

RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok

DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work. 
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB

QUIT

221 2.0.0 Bye
Connection closed by foreign host.
```

La cabecera del correo electrónico es portadora de una gran cantidad de información interesante en un correo electrónico. Entre otras cosas, proporciona información sobre el remitente y el destinatario, el momento de envío y llegada, las estaciones por las que pasó el correo electrónico en su camino, el contenido y formato del mensaje, y el remitente y el destinatario.

Algunos de estos datos son obligatorios, como la información del remitente y la fecha en que se creó el correo electrónico. Otra información es opcional. Sin embargo, la cabecera del correo electrónico no contiene información necesaria para la entrega técnica. Se transmite como parte del protocolo de transmisión. Tanto el remitente como el destinatario pueden acceder a la cabecera de un correo electrónico, aunque no es visible a primera vista. La estructura de una cabecera de correo electrónico está definida por la RFC 5322.

## Configuraciones Peligrosas

Para evitar que los correos electrónicos enviados sean filtrados por filtros de spam y no lleguen al destinatario, el remitente puede usar un servidor de retransmisión que el destinatario confíe. Este es un servidor SMTP que es conocido y verificado por todos los demás. Por lo general, el remitente debe autenticarse en el servidor de retransmisión antes de usarlo.

A menudo, los administradores no tienen una visión clara de qué rangos de IP deben permitir. Esto resulta en una configuración incorrecta del servidor SMTP que a menudo encontramos en pruebas de penetración externas e internas. Por lo tanto, permiten todas las direcciones IP para no causar errores en el tráfico de correo electrónico y así no perturbar ni interrumpir la comunicación con clientes potenciales y actuales.

### Configuración de Relay Abierto

```plaintext
mynetworks = 0.0.0.0/0
```

Con esta configuración, el servidor SMTP puede enviar correos electrónicos falsos e iniciar la comunicación entre múltiples partes. Otra posibilidad de ataque sería falsificar el correo electrónico y leerlo.

## Footprinting the Service

Los scripts predeterminados de Nmap incluyen `smtp-commands`, que utiliza el comando EHLO para enumerar todos los comandos posibles que se pueden ejecutar en el servidor SMTP de destino.

### Nmap

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.14.128 -sC -sV -p25

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.09 seconds
```

Sin embargo, también podemos usar el script NSE `smtp-open-relay` para identificar si el servidor SMTP objetivo es un relay abierto mediante 16 pruebas diferentes. Si también imprimimos la salida del escaneo en detalle, podremos ver qué pruebas está ejecutando el script.

### Nmap - Relay Abierto

Para detectar si un servidor SMTP es un relay abierto, podemos utilizar el script `smtp-open-relay` de Nmap. Este script realiza 16 pruebas diferentes para determinar si el servidor permite el reenvío de correos electrónicos desde cualquier dirección IP.

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-30 02:29 CEST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Initiating ARP Ping Scan at 02:29
Scanning 10.129.14.128 [1 port]
Completed ARP Ping Scan at 02:29, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 02:29
Completed Parallel DNS resolution of 1 host. at 02:29, 0.03s elapsed
Initiating SYN Stealth Scan at 02:29
Scanning 10.129.14.128 [1 port]
Discovered open port 25/tcp on 10.129.14.128
Completed SYN Stealth Scan at 02:29, 0.06s elapsed (1 total ports)
NSE: Script scanning 10.129.14.128.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.07s elapsed
Nmap scan report for 10.129.14.128
Host is up (0.00020s latency).

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-open-relay: Server is an open relay (16/16 tests)
|  MAIL FROM:<> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@nmap.scanme.org> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@ESMTP> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest%nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org"@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@[10.129.14.128]:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@ESMTP:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@[10.129.14.128]>
|_ MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@ESMTP>
MAC Address: 00:00:00:00:00:00 (VMware)

NSE: Script Post-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```

El resultado indica que el servidor SMTP está configurado como un relay abierto, ya que ha pasado todas las pruebas del script (16/16). Esto significa que el servidor permite el reenvío de correos electrónicos desde cualquier dirección IP, lo que puede ser un problema de seguridad significativo.
