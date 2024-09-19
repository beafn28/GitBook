# Fingerprinting

El **fingerprinting** se centra en extraer detalles técnicos sobre las tecnologías que impulsan un sitio web o una aplicación web. De manera similar a cómo una huella digital identifica de manera única a una persona, las firmas digitales de servidores web, sistemas operativos y componentes de software pueden revelar información crítica sobre la infraestructura de un objetivo y posibles debilidades de seguridad. Este conocimiento permite a los atacantes adaptar los ataques y explotar vulnerabilidades específicas de las tecnologías identificadas.

El fingerprinting sirve como un pilar fundamental del reconocimiento web por varias razones:

* **Ataques Dirigidos**: Conociendo las tecnologías específicas en uso, los atacantes pueden enfocar sus esfuerzos en exploits y vulnerabilidades que se sabe que afectan a esos sistemas. Esto aumenta significativamente las posibilidades de una compromisión exitosa.
* **Identificación de Configuraciones Incorrectas**: El fingerprinting puede exponer software mal configurado o desactualizado, configuraciones por defecto u otras debilidades que podrían no ser evidentes a través de otros métodos de reconocimiento.
* **Priorización de Objetivos**: Cuando se enfrentan a múltiples objetivos potenciales, el fingerprinting ayuda a priorizar los esfuerzos al identificar sistemas que son más propensos a ser vulnerables o que contienen información valiosa.
* **Construcción de un Perfil Integral**: La combinación de datos de fingerprinting con otros hallazgos de reconocimiento crea una vista holística de la infraestructura del objetivo, ayudando a entender su postura de seguridad general y los posibles vectores de ataque.

## Técnicas de Fingerprinting

Existen varias técnicas utilizadas para el fingerprinting de servidores web y tecnologías:

* **Banner Grabbing**: El banner grabbing implica analizar los banners presentados por los servidores web y otros servicios. Estos banners a menudo revelan el software del servidor, números de versión y otros detalles.
* **Análisis de Encabezados HTTP**: Los encabezados HTTP transmitidos con cada solicitud y respuesta web contienen una gran cantidad de información. El encabezado Server típicamente revela el software del servidor web, mientras que el encabezado X-Powered-By puede revelar tecnologías adicionales como lenguajes de scripting o frameworks.
* **Sondeo de Respuestas Específicas**: Enviar solicitudes especialmente diseñadas al objetivo puede provocar respuestas únicas que revelan tecnologías o versiones específicas. Por ejemplo, ciertos mensajes de error o comportamientos son característicos de determinados servidores web o componentes de software.
* **Análisis del Contenido de la Página**: El contenido de una página web, incluyendo su estructura, scripts y otros elementos, a menudo puede proporcionar pistas sobre las tecnologías subyacentes. Puede haber un encabezado de copyright que indique el software específico que se está utilizando, por ejemplo.

### Herramientas de Fingerprinting

Existen diversas herramientas que automatizan el proceso de fingerprinting, combinando varias técnicas para identificar servidores web, sistemas operativos, sistemas de gestión de contenido y otras tecnologías:

| **Herramienta** | **Descripción**                                                                                                                                 | **Características**                                                                                                   |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **Wappalyzer**  | Extensión de navegador y servicio en línea para el perfilado de tecnologías web.                                                                | Identifica una amplia gama de tecnologías web, incluyendo CMSs, frameworks, herramientas de análisis y más.           |
| **BuiltWith**   | Perfilador de tecnologías web que proporciona informes detallados sobre la pila tecnológica de un sitio web.                                    | Ofrece planes gratuitos y de pago con diferentes niveles de detalle.                                                  |
| **WhatWeb**     | Herramienta de línea de comandos para el fingerprinting de sitios web.                                                                          | Utiliza una vasta base de datos de firmas para identificar diversas tecnologías web.                                  |
| **Nmap**        | Escáner de red versátil que se puede usar para diversas tareas de reconocimiento, incluyendo fingerprinting de servicios y sistemas operativos. | Puede ser usado con scripts (NSE) para realizar fingerprinting más especializado.                                     |
| **Netcraft**    | Ofrece una variedad de servicios de seguridad web, incluyendo fingerprinting de sitios web e informes de seguridad.                             | Proporciona informes detallados sobre la tecnología de un sitio web, proveedor de alojamiento y postura de seguridad. |
| **wafw00f**     | Herramienta de línea de comandos diseñada específicamente para identificar Firewalls de Aplicaciones Web (WAFs).                                | Ayuda a determinar si está presente un WAF y, si es así, su tipo y configuración.                                     |

***

Espero que esta traducción y formato en Markdown te sean útiles para comprender mejor el fingerprinting en la web y las herramientas asociadas.

## Fingerprinting de inlanefreight.com

Vamos a aplicar nuestro conocimiento de fingerprinting para descubrir el ADN digital de nuestro host específico, `inlanefreight.com`. Utilizaremos técnicas tanto manuales como automatizadas para recopilar información sobre su servidor web, tecnologías y posibles vulnerabilidades.

#### Banner Grabbing

Nuestro primer paso es recopilar información directamente del servidor web. Podemos hacer esto utilizando el comando `curl` con el flag `-I` (o `--head`) para obtener solo los encabezados HTTP, no el contenido completo de la página.

```bash
sherlock28@htb[/htb]$ curl -I inlanefreight.com
```

La salida incluirá el banner del servidor, revelando el software del servidor web y el número de versión:

```plaintext
HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1
```

En este caso, vemos que `inlanefreight.com` está ejecutando `Apache/2.4.41`, específicamente la versión de Ubuntu. Esta información es nuestra primera pista, sugiriendo la pila tecnológica subyacente. También está intentando redirigir a `https://inlanefreight.com/`, así que obtengamos esos banners también.

### Banner Grabbing para Redirección HTTPS

```bash
sherlock28@htb[/htb]$ curl -I https://inlanefreight.com
```

```plaintext
HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8
```

Ahora obtenemos un encabezado realmente interesante, el servidor está tratando de redirigirnos nuevamente, pero esta vez vemos que es WordPress quien está realizando la redirección a `https://www.inlanefreight.com/`.

#### Banner Grabbing para el Dominio Redirigido

```bash
sherlock28@htb[/htb]$ curl -I https://www.inlanefreight.com
```

```plaintext
HTTP/1.1 200 OK
Date: Fri, 31 May 2024 12:12:26 GMT
Server: Apache/2.4.41 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8
```

Vemos algunos encabezados interesantes adicionales, incluyendo una ruta que contiene `wp-json`. El prefijo `wp-` es común en WordPress.

### Detección de Firewall de Aplicaciones Web (WAF)

Los Firewalls de Aplicaciones Web (WAFs) son soluciones de seguridad diseñadas para proteger aplicaciones web de varios ataques. Antes de proceder con más fingerprinting, es crucial determinar si `inlanefreight.com` emplea un WAF, ya que podría interferir con nuestros sondeos o potencialmente bloquear nuestras solicitudes.

Para detectar la presencia de un WAF, utilizaremos la herramienta `wafw00f`. Para instalar `wafw00f`, puedes usar pip3:

```bash
sherlock28@htb[/htb]$ pip3 install git+https://github.com/EnableSecurity/wafw00f
```

Una vez instalado, pasa el dominio que deseas verificar como argumento a la herramienta:

```bash
sherlock28@htb[/htb]$ wafw00f inlanefreight.com
```

```plaintext
                ______
               /      \
              (  W00f! )
               \  ____/
               ,,    __            404 Hack Not Found
           |`-.__   / /                      __     __
           /"  _/  /_/                       \ \   / /
          *===*    /                          \ \_/ /  405 Not Allowed
         /     )__//                           \   /
    /|  /     /---`                        403 Forbidden
    \\/`   \ |                                 / _ \
    `\    /_\\_              502 Bad Gateway  / / \ \  500 Internal Error
      `_____``-`                             /_/   \_\

                        ~ WAFW00F : v2.2.0 ~
        The Web Application Firewall Fingerprinting Toolkit
    
[*] Checking https://inlanefreight.com
[+] The site https://inlanefreight.com is behind Wordfence (Defiant) WAF.
[~] Number of requests: 2
```

El escaneo de `wafw00f` en `inlanefreight.com` revela que el sitio está protegido por el Firewall de Aplicaciones Web (WAF) Wordfence, desarrollado por Defiant.

Esto significa que el sitio tiene una capa de seguridad adicional que podría bloquear o filtrar nuestros intentos de reconocimiento. En un escenario real, sería crucial tener esto en cuenta al continuar con la investigación, ya que podrías necesitar adaptar técnicas para evadir o superar los mecanismos de detección del WAF.

### Nikto

Nikto es un potente escáner de servidores web de código abierto. Además de su función principal como herramienta de evaluación de vulnerabilidades, las capacidades de fingerprinting de Nikto proporcionan información sobre la pila tecnológica de un sitio web.

Nikto está preinstalado en pwnbox, pero si necesitas instalarlo, puedes ejecutar los siguientes comandos:

#### Instalación de Nikto

```bash
sherlock28@htb[/htb]$ sudo apt update && sudo apt install -y perl
sherlock28@htb[/htb]$ git clone https://github.com/sullo/nikto
sherlock28@htb[/htb]$ cd nikto/program
sherlock28@htb[/htb]$ chmod +x ./nikto.pl
```

Para escanear `inlanefreight.com` utilizando Nikto y ejecutar solo los módulos de fingerprinting, ejecuta el siguiente comando:

#### Escaneo con Nikto

```bash
sherlock28@htb[/htb]$ nikto -h inlanefreight.com -Tuning b
```

El flag `-h` especifica el host de destino. El flag `-Tuning b` le dice a Nikto que solo ejecute los módulos de Identificación de Software.

Nikto iniciará una serie de pruebas, intentando identificar software desactualizado, archivos o configuraciones inseguras y otros posibles riesgos de seguridad.

#### Resultados del Escaneo

```plaintext
- Nikto v2.5.0
--------------------------------------------------------------------------- 
+ Multiple IPs found: 134.209.24.248, 2a03:b0c0:1:e0::32c:b001 
+ Target IP:          134.209.24.248 
+ Target Hostname:    www.inlanefreight.com 
+ Target Port:        443 
--------------------------------------------------------------------------- 
+ SSL Info:        Subject:  /CN=inlanefreight.com 
                   Altnames: inlanefreight.com, www.inlanefreight.com 
                   Ciphers:  TLS_AES_256_GCM_SHA384 
                   Issuer:   /C=US/O=Let's Encrypt/CN=R3 
+ Start Time:         2024-05-31 13:35:54 (GMT0) 
--------------------------------------------------------------------------- 
+ Server: Apache/2.4.41 (Ubuntu) 
+ /: Link header found with value: ARRAY(0x558e78790248). See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link 
+ /: The site uses TLS and the Strict-Transport-Security HTTP header is not defined. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security 
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/ 
+ /index.php?: Uncommon header 'x-redirect-by' found, with contents: WordPress. 
+ No CGI Directories found (use '-C all' to force check all possible dirs) 
+ /: The Content-Encoding header is set to "deflate" which may mean that the server is vulnerable to the BREACH attack. See: http://breachattack.com/ 
+ Apache/2.4.41 appears to be outdated (current is at least 2.4.59). Apache 2.2.34 is the EOL for the 2.x branch. 
+ /: Web Server returns a valid response with junk HTTP methods which may cause false positives. 
+ /license.txt: License file found may identify site software. 
+ /: A Wordpress installation was found. 
+ /wp-login.php?action=register: Cookie wordpress_test_cookie created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies 
+ /wp-login.php:X-Frame-Options header is deprecated and has been replaced with the Content-Security-Policy HTTP header with the frame-ancestors directive instead. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options 
+ /wp-login.php: Wordpress login found. 
+ 1316 requests: 0 error(s) and 12 item(s) reported on remote host 
+ End Time:           2024-05-31 13:47:27 (GMT0) (693 seconds) 
--------------------------------------------------------------------------- 
+ 1 host(s) tested
```

#### Resultados Clave del Escaneo

* **IPs**: El sitio web se resuelve a direcciones IPv4 (134.209.24.248) e IPv6 (2a03:b0c0:1:e0::32c).
* **Tecnología del Servidor**: El sitio web utiliza Apache/2.4.41 (Ubuntu).
* **Presencia de WordPress**: El escaneo identificó una instalación de WordPress, incluyendo la página de inicio de sesión (/wp-login.php). Esto sugiere que el sitio podría ser un objetivo potencial para exploits comunes relacionados con WordPress.
* **Divulgación de Información**: La presencia de un archivo `license.txt` podría revelar detalles adicionales sobre los componentes del software del sitio web.
* **Encabezados**: Se encontraron varios encabezados no estándar o inseguros, incluyendo un encabezado `Strict-Transport-Security` ausente y un encabezado `x-redirect-by` potencialmente inseguro.
