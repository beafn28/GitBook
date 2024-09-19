# Automating Recon

Mientras que la reconstrucción manual puede ser efectiva, también puede ser lenta y propensa a errores humanos. Automatizar las tareas de reconocimiento web puede mejorar significativamente la eficiencia y precisión, permitiendo recopilar información a gran escala y identificar vulnerabilidades potenciales más rápidamente.

## **¿Por Qué Automatizar la Reconstrucción?**

La automatización ofrece varias ventajas clave para la reconstrucción web:

* **Eficiencia:** Las herramientas automatizadas pueden realizar tareas repetitivas mucho más rápido que los humanos, liberando tiempo valioso para el análisis y la toma de decisiones.
* **Escalabilidad:** La automatización permite escalar tus esfuerzos de reconstrucción a través de un gran número de objetivos o dominios, descubriendo un rango más amplio de información.
* **Consistencia:** Las herramientas automatizadas siguen reglas y procedimientos predefinidos, garantizando resultados consistentes y reproducibles y minimizando el riesgo de error humano.
* **Cobertura Completa:** La automatización se puede programar para realizar una amplia gama de tareas de reconstrucción, incluyendo enumeración DNS, descubrimiento de subdominios, rastreo web, escaneo de puertos, y más, asegurando una cobertura exhaustiva de los vectores de ataque potenciales.
* **Integración:** Muchos marcos de automatización permiten una fácil integración con otras herramientas y plataformas, creando un flujo de trabajo sin fisuras desde la reconstrucción hasta la evaluación de vulnerabilidades y explotación.

## **Marcos de Reconstrucción**

Estos marcos proporcionan un conjunto completo de herramientas para la reconstrucción web:

* **FinalRecon:** Una herramienta de reconstrucción basada en Python que ofrece una gama de módulos para diferentes tareas como verificación de certificados SSL, recopilación de información Whois, análisis de encabezados y rastreo. Su estructura modular permite una fácil personalización para necesidades específicas.
* **Recon-ng:** Un potente marco escrito en Python que ofrece una estructura modular con varios módulos para diferentes tareas de reconstrucción. Puede realizar enumeración DNS, descubrimiento de subdominios, escaneo de puertos, rastreo web e incluso explotar vulnerabilidades conocidas.
* **theHarvester:** Diseñado específicamente para recopilar direcciones de correo electrónico, subdominios, hosts, nombres de empleados, puertos abiertos y banners desde diversas fuentes públicas como motores de búsqueda, servidores de claves PGP y la base de datos SHODAN. Es una herramienta de línea de comandos escrita en Python.
* **SpiderFoot:** Una herramienta de automatización de inteligencia de código abierto que se integra con varias fuentes de datos para recopilar información sobre un objetivo, incluyendo direcciones IP, nombres de dominio, direcciones de correo electrónico y perfiles en redes sociales. Puede realizar búsquedas DNS, rastreo web, escaneo de puertos, y más.
* **OSINT Framework:** Una colección de diversas herramientas y recursos para la recopilación de inteligencia de código abierto. Cubre una amplia gama de fuentes de información, incluyendo redes sociales, motores de búsqueda, registros públicos, y más.

### **FinalRecon**

FinalRecon ofrece una gran cantidad de información de recon:

* **Información de Encabezado:** Revela detalles del servidor, tecnologías usadas y posibles configuraciones de seguridad incorrectas.
* **Búsqueda Whois:** Descubre detalles de registro del dominio, incluyendo información del registrante y detalles de contacto.
* **Información de Certificado SSL:** Examina el certificado SSL/TLS en cuanto a validez, emisor y otros detalles relevantes.
* **Rastreo:**
  * **HTML, CSS, JavaScript:** Extrae enlaces, recursos y posibles vulnerabilidades de estos archivos.
  * **Enlaces Internos/Externos:** Mapea la estructura del sitio web e identifica conexiones con otros dominios.
  * **Imágenes, robots.txt, sitemap.xml:** Recoge información sobre rutas permitidas/no permitidas de rastreo y estructura del sitio web.
  * **Enlaces en JavaScript, Wayback Machine:** Descubre enlaces ocultos y datos históricos del sitio web.
* **Enumeración DNS:** Consulta más de 40 tipos de registros DNS, incluyendo registros DMARC para la evaluación de seguridad del correo electrónico.
* **Enumeración de Subdominios:** Aprovecha múltiples fuentes de datos (crt.sh, AnubisDB, ThreatMiner, CertSpotter, API de Facebook, API de VirusTotal, API de Shodan, API de BeVigil) para descubrir subdominios.
* **Enumeración de Directorios:** Soporta listas de palabras personalizadas y extensiones de archivos para descubrir directorios y archivos ocultos.
* **Wayback Machine:** Recupera URLs de los últimos cinco años para analizar cambios en el sitio web y posibles vulnerabilidades.

**Instalación Rápida y Fácil:**

Para comenzar, primero clona el repositorio de FinalRecon desde GitHub usando:

```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
```

Luego, navega al directorio creado:

```bash
cd FinalRecon
```

Instala las dependencias necesarias de Python usando:

```bash
pip3 install -r requirements.txt
```

Para asegurarte de que el script principal sea ejecutable, cambia los permisos del archivo:

```bash
chmod +x ./finalrecon.py
```

Finalmente, puedes verificar que FinalRecon está instalado correctamente y obtener una descripción general de sus opciones ejecutando:

```bash
./finalrecon.py --help
```

Esto mostrará un mensaje de ayuda con detalles sobre cómo usar la herramienta, incluyendo los diversos módulos y sus opciones respectivas.

**Ejemplo de Uso:**

Si queremos que FinalRecon recopile información de encabezado y realice una búsqueda Whois para `inlanefreight.com`, usaríamos las banderas correspondientes (`--headers` y `--whois`), por lo que el comando sería:

```bash
./finalrecon.py --headers --whois --url http://inlanefreight.com
```

Esto generará una salida similar a:

```yaml
 ______  __   __   __   ______   __
/\  ___\/\ \ /\ "-.\ \ /\  __ \ /\ \
\ \  __\\ \ \\ \ \-.  \\ \  __ \\ \ \____
 \ \_\   \ \_\\ \_\\"\_\\ \_\ \_\\ \_____\
  \/_/    \/_/ \/_/ \/_/ \/_/\/_/ \/_____/
 ______   ______   ______   ______   __   __
/\  == \ /\  ___\ /\  ___\ /\  __ \ /\ "-.\ \
\ \  __< \ \  __\ \ \ \____\ \ \/\ \\ \ \-.  \
 \ \_\ \_\\ \_____\\ \_____\\ \_____\\ \_\\"\_\
  \/_/ /_/ \/_____/ \/_____/ \/_____/ \/_/ \/_/

[>] Created By   : thewhiteh4t
 |---> Twitter   : https://twitter.com/thewhiteh4t
 |---> Community : https://twc1rcle.com/
[>] Version      : 1.1.6

[+] Target : http://inlanefreight.com

[+] IP Address : 134.209.24.248

[!] Headers :

Date : Tue, 11 Jun 2024 10:08:00 GMT
Server : Apache/2.4.41 (Ubuntu)
Link : <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/", <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json", <https://www.inlanefreight.com/>; rel=shortlink
Vary : Accept-Encoding
Content-Encoding : gzip
Content-Length : 5483
Keep-Alive : timeout=5, max=100
Connection : Keep-Alive
Content-Type : text/html; charset=UTF-8

[!] Whois Lookup :

   Domain Name: INLANEFREIGHT.COM
   Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.registrar.amazon.com
   Registrar URL: http://registrar.amazon.com
   Updated Date: 2023-07-03T01:11:15Z
   Creation Date: 2019-08-05T22:43:09Z
   Registry Expiry Date: 2024-08-05T22:43:09Z
   Registrar: Amazon Registrar, Inc.
   Registrar IANA ID: 468
   Registrar Abuse Contact Email: abuse@amazonaws.com
   Registrar Abuse Contact Phone: +1.2024422253
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Name Server: NS-1303.AWSDNS-34.ORG
   Name Server: NS-1580.AWSDNS-05.CO.UK
   Name Server: NS-161.AWSDNS-20.COM
   Name Server: NS-671.AWSDNS-19.NET
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/


[+] Completed in 0:00:00.257780

[+] Exported : /home/htb-ac-643601/.local/share/finalrecon/dumps/fr_inlanefreight.com_11-06-2024_11:07:59
```
