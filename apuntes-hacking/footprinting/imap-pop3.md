# IMAP/POP3

Con la ayuda del Protocolo de Acceso a Mensajes de Internet (IMAP), es posible acceder a correos electrónicos desde un servidor de correo. A diferencia del Protocolo de Oficina de Correos (POP3), IMAP permite la gestión en línea de los correos electrónicos directamente en el servidor y soporta estructuras de carpetas. Así, es un protocolo de red para la gestión en línea de correos electrónicos en un servidor remoto. El protocolo está basado en cliente-servidor y permite la sincronización de un cliente de correo local con el buzón en el servidor, proporcionando una especie de sistema de archivos en red para correos electrónicos, permitiendo una sincronización sin problemas a través de varios clientes independientes. Por otro lado, POP3 no tiene la misma funcionalidad que IMAP, ya que solo proporciona funciones para listar, recuperar y eliminar correos electrónicos en el servidor de correo. Por lo tanto, se deben utilizar protocolos como IMAP para funcionalidades adicionales como buzones jerárquicos directamente en el servidor de correo, acceso a múltiples buzones durante una sesión y preselección de correos electrónicos.

Los clientes acceden a estas estructuras en línea y pueden crear copias locales. Incluso a través de varios clientes, esto resulta en una base de datos uniforme. Los correos electrónicos permanecen en el servidor hasta que se eliminan. IMAP es un protocolo basado en texto y tiene funciones extendidas, como la navegación de correos electrónicos directamente en el servidor. También es posible que varios usuarios accedan al servidor de correo simultáneamente. Sin una conexión activa al servidor, la gestión de correos electrónicos es imposible. Sin embargo, algunos clientes ofrecen un modo fuera de línea con una copia local del buzón. El cliente sincroniza todos los cambios locales realizados fuera de línea cuando se restablece la conexión.

El cliente establece la conexión con el servidor a través del puerto 143. Para la comunicación, utiliza comandos basados en texto en formato ASCII. Se pueden enviar varios comandos en sucesión sin esperar confirmación del servidor. Las confirmaciones posteriores del servidor pueden asignarse a los comandos individuales utilizando los identificadores enviados junto con los comandos. Inmediatamente después de establecerse la conexión, el usuario se autentica en el servidor mediante nombre de usuario y contraseña. El acceso al buzón deseado solo es posible después de una autenticación exitosa.

SMTP se usa generalmente para enviar correos electrónicos. Al copiar los correos electrónicos enviados en una carpeta IMAP, todos los clientes tienen acceso a todos los correos enviados, independientemente del ordenador desde el que se hayan enviado. Otra ventaja del Protocolo de Acceso a Mensajes de Internet es la creación de carpetas personales y estructuras de carpetas en el buzón. Esta característica hace que el buzón sea más claro y fácil de gestionar. Sin embargo, el requerimiento de espacio de almacenamiento en el servidor de correo aumenta.

Sin medidas adicionales, IMAP funciona sin cifrado y transmite comandos, correos electrónicos o nombres de usuario y contraseñas en texto plano. Muchos servidores de correo requieren el establecimiento de una sesión IMAP cifrada para garantizar una mayor seguridad en el tráfico de correos electrónicos y prevenir el acceso no autorizado a los buzones. Para esto, se utiliza comúnmente SSL/TLS. Dependiendo del método y la implementación utilizados, la conexión cifrada usa el puerto estándar 143 o un puerto alternativo como el 993.

## Configuración por Defecto

Tanto IMAP como POP3 tienen una gran cantidad de opciones de configuración, lo que hace difícil profundizar en cada componente en más detalle. Si deseas examinar estas configuraciones de protocolo más a fondo, te recomendamos crear una máquina virtual local e instalar los paquetes `dovecot-imapd` y `dovecot-pop3d` usando `apt`, y experimentar con las configuraciones.

En la documentación de Dovecot, puedes encontrar los ajustes básicos individuales y las opciones de configuración del servicio que se pueden utilizar para nuestros experimentos. Sin embargo, echemos un vistazo a la lista de comandos y veamos cómo podemos interactuar y comunicarnos directamente con IMAP y POP3 usando la línea de comandos.

### Comandos IMAP

| Comando                       | Descripción                                                                                                      |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `LOGIN username password`     | Autenticación del usuario.                                                                                       |
| `LIST "" *`                   | Lista todos los directorios.                                                                                     |
| `CREATE "INBOX"`              | Crea un buzón con el nombre especificado.                                                                        |
| `DELETE "INBOX"`              | Elimina un buzón.                                                                                                |
| `RENAME "ToRead" "Important"` | Renombra un buzón.                                                                                               |
| `LSUB "" *`                   | Devuelve un subconjunto de nombres del conjunto de nombres que el Usuario ha declarado como activos o suscritos. |
| `SELECT INBOX`                | Selecciona un buzón para que los mensajes en el buzón puedan ser accedidos.                                      |
| `UNSELECT INBOX`              | Sale del buzón seleccionado.                                                                                     |
| `FETCH <ID> all`              | Recupera datos asociados con un mensaje en el buzón.                                                             |
| `CLOSE`                       | Elimina todos los mensajes con la etiqueta Deleted establecida.                                                  |
| `LOGOUT`                      | Cierra la conexión con el servidor IMAP.                                                                         |

### Comandos POP3

| Comando         | Descripción                                                    |
| --------------- | -------------------------------------------------------------- |
| `USER username` | Identifica al usuario.                                         |
| `PASS password` | Autenticación del usuario usando su contraseña.                |
| `STAT`          | Solicita el número de correos guardados en el servidor.        |
| `LIST`          | Solicita al servidor el número y tamaño de todos los correos.  |
| `RETR id`       | Solicita al servidor que entregue el correo solicitado por ID. |
| `DELE id`       | Solicita al servidor que elimine el correo solicitado por ID.  |
| `CAPA`          | Solicita al servidor que muestre las capacidades del servidor. |
| `RSET`          | Solicita al servidor que reinicie la información transmitida.  |
| `QUIT`          | Cierra la conexión con el servidor POP3.                       |

## Configuraciones Peligrosas

No obstante, las opciones de configuración mal configuradas podrían permitirnos obtener más información, como depurar los comandos ejecutados en el servicio o iniciar sesión como anónimo, similar al servicio FTP. La mayoría de las empresas utilizan proveedores de correo electrónico de terceros como Google, Microsoft, entre otros. Sin embargo, algunas empresas todavía utilizan sus propios servidores de correo por varias razones diferentes. Una de estas razones es mantener la privacidad que desean tener en sus propias manos. Muchos errores de configuración pueden ser cometidos por los administradores, lo que en los peores casos permitirá leer todos los correos enviados y recibidos, que incluso pueden contener información confidencial o sensible. Algunas de estas opciones de configuración incluyen:

| Configuración             | Descripción                                                                                                 |
| ------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `auth_debug`              | Habilita el registro de depuración de autenticación.                                                        |
| `auth_debug_passwords`    | Esta configuración ajusta la verbosidad del registro, y las contraseñas enviadas y el esquema se registran. |
| `auth_verbose`            | Registra los intentos de autenticación fallidos y sus razones.                                              |
| `auth_verbose_passwords`  | Las contraseñas usadas para la autenticación se registran y también pueden ser truncadas.                   |
| `auth_anonymous_username` | Especifica el nombre de usuario que se usará al iniciar sesión con el mecanismo SASL ANONYMOUS.             |

## Footprinting del Servicio

Por defecto, los puertos 110 y 995 se utilizan para POP3, y los puertos 143 y 993 se utilizan para IMAP. Los puertos más altos (993 y 995) utilizan TLS/SSL para cifrar la comunicación entre el cliente y el servidor. Usando Nmap, podemos escanear el servidor para estos puertos. El escaneo devolverá la información correspondiente (como se muestra a continuación) si el servidor utiliza un certificado embebido.

### Nmap

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00026s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
143/tcp open  imap     Dovecot imapd
|_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
993/tcp open  ssl/imap Dovecot imapd
|_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds
```

Por ejemplo, a partir de la salida, podemos ver que el nombre común es `mail1.inlanefreight.htb`, y el servidor de correo pertenece a la organización Inlanefreight, que está ubicada en California. Las capacidades mostradas nos indican los comandos disponibles en el servidor y para el servicio en el puerto correspondiente.

Si logramos obtener las credenciales de acceso de uno de los empleados, un atacante podría iniciar sesión en el servidor de correo y leer o incluso enviar los mensajes individuales.

### cURL

```bash
sherlock28@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd

* LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." INBOX
```

Si también usamos la opción verbose (`-v`), veremos cómo se realiza la conexión. A partir de esto, podemos ver la versión de TLS utilizada para el cifrado, detalles adicionales del certificado SSL e incluso el banner, que a menudo contiene la versión del servidor de correo.

```bash
sherlock28@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v

*   Trying 10.129.14.128:993...
* TCP_NODELAY set
* Connected to 10.129.14.128 (10.129.14.128) port 993 (#0)
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* Server certificate:
*  subject: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  start date: Sep 19 19:44:58 2021 GMT
*  expire date: Jul  4 19:44:58 2295 GMT
*  issuer: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
< * OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
> A001 CAPABILITY
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN
< A001 OK Pre-login capabilities listed, post-login capabilities have more.
> A002 AUTHENTICATE PLAIN AGNyeTBsMXQzADEyMzQ=
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE
< A002 OK Logged in
> A003 LIST "" *
< * LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." Important
< * LIST (\HasNoChildren) "." INBOX
* LIST (\HasNoChildren) "." INBOX
< A003 OK List completed (0.001 + 0.000 secs).
* Connection #0 to host 10.129.14.128 left intact
```

Para interactuar con el servidor IMAP o POP3 sobre SSL, podemos usar `openssl`, así como `ncat`. Los comandos para esto se verían así:

### OpenSSL - Interacción Cifrada por TLS POP3

```bash
sherlock28@htb[/htb]$ openssl s_client -connect 10.129.14.128:pop3s

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb

...SNIP...

---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 3CC39A7F2928B252EF2FFA5462140B1A0A74B29D4708AA8DE1515BB4033D92C2
    Session-ID-ctx:
    Resumption PSK: 68419D933B5FEBD878FF1BA399A926813BEA3652555E05F0EC75D65819A263AA25FA672F8974C37F6446446BB7EA83F9
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - d7 86 ac 7e f3 f4 95 35-88 40 a5 b5 d6 a6 41 e4   ...~...5.@....A.
    0010 - 96 6c e6 12 4f 50 ce 72-36 25 df e1 72 d9 23 94   .l..OP.r6%..r.#.
    0020 - cc 29 90 08 58 1b 57 ab-db a8 6b f7 8f 31 5b ad   .)..X.W...k..1[.
    0030 - 47 94 f4 67 58 1f 96 d9-ca ca 56 f9 7a 12 f6 6d   G..gX.....V.z..m
    0040 - 43 b9 b6 68 de db b2 47-4f 9f 48 14 40 45 8f 89   C..h...GO.H.@E..
    0050 - fa 19 35 9c 6d 3c a1 46-5c a2 65 ab 87 a4 fd 5e   ..5.m<.F\.e....^
    0060 - a2 95 25 d4 43 b8 71 70-40 6c fe 6f 0e d1 a0 38   ..%.C.qp@l.o...8
    0070 - 6e bd 73 91 ed 05 89 83-f5 3e d9 2a e0 2e 96 f8   n.s......>.*....
    0080 - 99 f0 50 15 e0 1b 66 db-7c 9f 10 80 4a a1 8b 24   ..P...f.|...J..$
    0090 - bb 00 03 d4 93 2b d9 95-64 44 5b c2 6b 2e 01 b5   .....+..dD[.k...
    00a0 - e8 1b f4 a4 98 a7 7a 7d-0a 80 cc 0a ad fe 6e b3   ......z}......n.
    00b0 - 0a d6 50 5d fd 9a b4 5c-28 a4 c9 36 e4 7d 2a 1e   ..P]...\(..6.}*.

    Start Time: 1632081313
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
+OK HTB-Academy POP3 Server
```

### OpenSSL - Interacción Cifrada por TLS POP3

```bash
sherlock28@htb[/htb]$ openssl s_client -connect 10.129.14.128:imaps

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb

...SNIP...

---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 2B7148CD1B7B92BA123E06E22831FCD3B365A5EA06B2CDEF1A5F397177130699
    Session-ID-ctx:
    Resumption PSK: 4D9F082C6660646C39135F9996DDA2C199C4F7E75D65FA5303F4A0B274D78CC5BD3416C8AF50B31A34EC022B619CC633
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - 68 3b b6 68 ff 85 95 7c-8a 8a 16 b2 97 1c 72 24   h;.h...|......r$
    0010 - 62 a7 84 ff c3 24 ab 99-de 45 60 26 e7 04 4a 7d   b....$...E`&..J}
    0020 - bc 6e 06 a0 ff f7 d7 41-b5 1b 49 9c 9f 36 40 8d   .n.....A..I..6@.
    0030 - 93 35 ed d9 eb 1f 14 d7-a5 f6 3f c8 52 fb 9f 29   .5........?.R..)
    0040 - 89 8d de e6 46 95 b3 32-48 80 19 bc 46 36 cb eb   ....F..2H...F6..
    0050 - 35 79 54 4c 57 f8 ee 55-06 e3 59 7f 5e 64 85 b0   5yTLW..U..Y.^d..
    0060 - f3 a4 8c a6 b6 47 e4 59-ee c9 ab 54 a4 ab 8c 01   .....G.Y...T....
    0070 - 56 bb b9 bb 3b f6 96 74-16 c9 66 e2 6c 28 c6 12   V...;..t..f.l(.
    0080 - 34 c7 63 6b ff 71 16 7f-91 69 dc 38 7a 47 46 ec   4.ck.q...i.8zGF.
    0090 - 67 b7 a2 90 8b 31 58 a0-4f 57 30 6a b6 2e 3a 21   g....1X.OW0j..:!
    00a0 - 54 c7 ba f0 a9 74 13 11-d5 d1 ec cc ea f9 54 7d   T....t........T}
    00b0 - 46 a6 33 ed 5d 24 ed b0-20 63 43 d8 8f 14 4d 62   F.3.]$.. cC...Mb

    Start Time: 1632081604
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
```

Una vez que hemos iniciado con éxito una conexión y hemos iniciado sesión en el servidor de correo de destino, podemos usar los comandos anteriores para trabajar con y navegar por el servidor. Queremos señalar que la configuración de nuestro propio servidor de correo, la investigación sobre él y los experimentos que podemos realizar junto con otros miembros de la comunidad nos proporcionarán el conocimiento necesario para entender la comunicación que está teniendo lugar y qué opciones de configuración son responsables de ello.

En la sección de SMTP, encontramos al usuario `robin`. Otro miembro de nuestro equipo descubrió que el usuario también utiliza su nombre de usuario como contraseña (`robin:robin`). Podemos usar estas credenciales y probarlas para interactuar con los servicios IMAP/POP3.
