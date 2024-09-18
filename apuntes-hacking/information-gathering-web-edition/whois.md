# WHOIS

WHOIS es un protocolo ampliamente utilizado de consulta y respuesta diseñado para acceder a bases de datos que almacenan información sobre recursos de internet registrados. Aunque se asocia principalmente con nombres de dominio, WHOIS también puede proporcionar detalles sobre bloques de direcciones IP y sistemas autónomos. Piensa en él como una gran guía telefónica de internet que te permite buscar quién posee o es responsable de varios activos en línea.

### **Ejemplo de WHOIS**

```bash
sherlock28@htb[/htb]$ whois inlanefreight.com

[...]
Domain Name: inlanefreight.com
Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.registrar.amazon
Registrar URL: https://registrar.amazon.com
Updated Date: 2023-07-03T01:11:15Z
Creation Date: 2019-08-05T22:43:09Z
[...]
```

Cada registro WHOIS normalmente contiene la siguiente información:

* **Domain Name**: El nombre de dominio (por ejemplo, example.com).
* **Registrar**: La empresa donde se registró el dominio (por ejemplo, GoDaddy, Namecheap).
* **Contacto del Registrante**: La persona u organización que registró el dominio.
* **Contacto Administrativo**: La persona responsable de la gestión del dominio.
* **Contacto Técnico**: La persona que maneja los problemas técnicos relacionados con el dominio.
* **Fechas de Creación y Expiración**: Cuándo se registró el dominio y cuándo está programado para expirar.
* **Servidores de Nombres**: Servidores que traducen el nombre de dominio a una dirección IP.

## Historia de WHOIS

La historia de WHOIS está intrínsecamente ligada a la visión y dedicación de Elizabeth Feinler, una científica informática que desempeñó un papel crucial en la formación del internet temprano.

En la década de 1970, Feinler y su equipo en el Centro de Información de Redes (NIC) del Instituto de Investigación de Stanford reconocieron la necesidad de un sistema para rastrear y gestionar el creciente número de recursos en ARPANET, el precursor del internet moderno. Su solución fue la creación del directorio WHOIS, una base de datos rudimentaria pero revolucionaria que almacenaba información sobre usuarios de la red, nombres de host y nombres de dominio.

## **¿Por qué WHOIS es importante para el reconocimiento web?**

Los datos de WHOIS son una mina de oro de información para los **testers de penetración** durante la fase de reconocimiento de una evaluación. Ofrecen valiosos conocimientos sobre la huella digital de la organización objetivo y posibles vulnerabilidades:

* **Identificación de Personal Clave**: Los registros WHOIS a menudo revelan nombres, direcciones de correo electrónico y números de teléfono de las personas responsables de gestionar el dominio. Esta información puede aprovecharse para ataques de ingeniería social o para identificar posibles objetivos en campañas de phishing.
* **Descubrimiento de Infraestructura de Red**: Los detalles técnicos, como servidores de nombres y direcciones IP, proporcionan pistas sobre la infraestructura de red del objetivo. Esto puede ayudar a los testers de penetración a identificar posibles puntos de entrada o configuraciones incorrectas.
* **Análisis de Datos Históricos**: El acceso a registros históricos de WHOIS a través de servicios como WhoisFreaks puede revelar cambios en la propiedad, información de contacto o detalles técnicos a lo largo del tiempo. Esto puede ser útil para rastrear la evolución de la presencia digital del objetivo.

## Escenario 1: Investigación de Phishing

Un gateway de seguridad de correo electrónico marca un correo sospechoso enviado a múltiples empleados dentro de una empresa. El correo afirma ser del banco de la compañía y urge a los destinatarios a hacer clic en un enlace para actualizar su información de cuenta. Un analista de seguridad investiga el correo y comienza realizando una búsqueda WHOIS del dominio vinculado en el correo.

El registro WHOIS revela lo siguiente:

* **Fecha de Registro**: El dominio fue registrado hace solo unos días.
* **Registrante**: La información del registrante está oculta detrás de un servicio de privacidad.
* **Servidores de Nombres**: Los servidores de nombres están asociados con un proveedor de hosting "bulletproof" conocido, a menudo usado para actividades maliciosas.

Esta combinación de factores genera importantes alertas para el analista. La reciente fecha de registro, la información del registrante oculta y el hosting sospechoso sugieren fuertemente una campaña de phishing. El analista alerta rápidamente al departamento de TI de la empresa para bloquear el dominio y advierte a los empleados sobre la estafa.

Una investigación más a fondo en el proveedor de hosting y las direcciones IP asociadas podría revelar otros dominios de phishing o infraestructura utilizada por el actor malicioso.

## Escenario 2: Análisis de Malware

Un investigador de seguridad está analizando una nueva cepa de malware que ha infectado varios sistemas dentro de una red. El malware se comunica con un servidor remoto para recibir comandos y exfiltrar datos robados. Para obtener información sobre la infraestructura del actor malicioso, el investigador realiza una búsqueda WHOIS del dominio asociado con el servidor de comando y control (C2).

El registro WHOIS revela lo siguiente:

* **Registrante**: El dominio está registrado a nombre de una persona que usa un servicio de correo gratuito conocido por ofrecer anonimato.
* **Ubicación**: La dirección del registrante está en un país con una alta prevalencia de delitos cibernéticos.
* **Registrador**: El dominio fue registrado a través de un registrador con un historial de políticas laxas contra abusos.

Basado en esta información, el investigador concluye que el servidor C2 probablemente esté alojado en un servidor comprometido o en un servidor "bulletproof". Luego utiliza los datos de WHOIS para identificar al proveedor de hosting y notificarle sobre la actividad maliciosa.

## Escenario 3: Informe de Inteligencia de Amenazas

Una firma de ciberseguridad rastrea las actividades de un grupo de actores maliciosos sofisticados conocidos por atacar instituciones financieras. Los analistas recopilan datos WHOIS sobre múltiples dominios asociados con campañas pasadas del grupo para compilar un informe de inteligencia de amenazas.

Al analizar los registros WHOIS, los analistas descubren los siguientes patrones:

* **Fechas de Registro**: Los dominios fueron registrados en grupos, a menudo poco antes de ataques importantes.
* **Registrantes**: Los registrantes usan varios alias e identidades falsas.
* **Servidores de Nombres**: Los dominios a menudo comparten los mismos servidores de nombres, lo que sugiere una infraestructura común.
* **Historial de Eliminaciones**: Muchos dominios han sido eliminados después de los ataques, lo que indica intervenciones previas de las fuerzas del orden o de seguridad.

Estos datos permiten a los analistas crear un perfil detallado de las tácticas, técnicas y procedimientos (TTP) del grupo de actores maliciosos. El informe incluye indicadores de compromiso (IOCs) basados en los datos WHOIS, que otras organizaciones pueden utilizar para detectar y bloquear futuros ataques.

## Uso de WHOIS

Antes de usar el comando `whois`, asegúrate de que esté instalado en tu sistema Linux. Es una utilidad disponible a través de los gestores de paquetes de Linux, y si no está instalado, puedes instalarlo fácilmente con:

```bash
sudo apt update
sudo apt install whois -y
```

La forma más sencilla de acceder a los datos WHOIS es a través de la herramienta de línea de comandos `whois`. Veamos un ejemplo realizando una búsqueda WHOIS en facebook.com:

```bash
sherlock28@htb[/htb]$ whois facebook.com

   Domain Name: FACEBOOK.COM
   Registry Domain ID: 2320948_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.registrarsafe.com
   Registrar URL: http://www.registrarsafe.com
   Updated Date: 2024-04-24T19:06:12Z
   Creation Date: 1997-03-29T05:00:00Z
   Registry Expiry Date: 2033-03-30T04:00:00Z
   Registrar: RegistrarSafe, LLC
   Registrar IANA ID: 3237
   Registrar Abuse Contact Email: abusecomplaints@registrarsafe.com
   Registrar Abuse Contact Phone: +1-650-308-7004
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Domain Status: serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited
   Domain Status: serverTransferProhibited https://icann.org/epp#serverTransferProhibited
   Domain Status: serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited
   Name Server: A.NS.FACEBOOK.COM
   Name Server: B.NS.FACEBOOK.COM
   Name Server: C.NS.FACEBOOK.COM
   Name Server: D.NS.FACEBOOK.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2024-06-01T11:24:10Z <<<
```

El resultado de WHOIS para facebook.com revela varios detalles clave:

**Registro del Dominio**

* **Registrador**: RegistrarSafe, LLC
* **Fecha de Creación**: 1997-03-29
* **Fecha de Expiración**: 2033-03-30

Estos detalles indican que el dominio está registrado con RegistrarSafe, LLC y ha estado activo durante un período considerable, lo que sugiere su legitimidad y presencia establecida en línea. La fecha de expiración lejana refuerza aún más su longevidad.

**Propietario del Dominio**

* **Organización Registrante/Administrativa/Técnica**: Meta Platforms, Inc.
* **Contacto Registrante/Administrativo/Técnico**: Domain Admin

Esta información identifica a Meta Platforms, Inc. como la organización detrás de facebook.com, y a "Domain Admin" como el punto de contacto para asuntos relacionados con el dominio. Esto es consistente con la expectativa de que Facebook, una plataforma de redes sociales prominente, es propiedad de Meta Platforms, Inc.

**Estado del Dominio**

* **clientDeleteProhibited, clientTransferProhibited, clientUpdateProhibited, serverDeleteProhibited, serverTransferProhibited, y serverUpdateProhibited**

Estos estados indican que el dominio está protegido contra cambios, transferencias o eliminaciones no autorizadas tanto del lado del cliente como del servidor. Esto destaca un fuerte énfasis en la seguridad y el control sobre el dominio.

**Servidores de Nombres**

* **A.NS.FACEBOOK.COM, B.NS.FACEBOOK.COM, C.NS.FACEBOOK.COM, D.NS.FACEBOOK.COM**

Estos servidores de nombres están todos dentro del dominio facebook.com, lo que sugiere que Meta Platforms, Inc. gestiona su infraestructura DNS. Es una práctica común para grandes organizaciones mantener el control y la fiabilidad sobre su resolución DNS.

En general, la salida de WHOIS para facebook.com se alinea con las expectativas para un dominio bien establecido y seguro propiedad de una gran organización como Meta Platforms, Inc.

Aunque el registro WHOIS proporciona información de contacto para problemas relacionados con el dominio, puede no ser directamente útil para identificar empleados individuales o vulnerabilidades específicas. Esto resalta la necesidad de combinar los datos WHOIS con otras técnicas de reconocimiento para comprender de manera integral la huella digital del objetivo.\
