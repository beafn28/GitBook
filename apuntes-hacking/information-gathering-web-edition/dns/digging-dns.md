# Digging DNS

Habiendo establecido una comprensión sólida de los fundamentos del DNS y sus diversos tipos de registros, ahora pasemos a la parte práctica. Esta sección explorará las herramientas y técnicas para aprovechar el DNS en el reconocimiento web.

### **Herramientas de DNS**

El reconocimiento de DNS implica utilizar herramientas especializadas diseñadas para consultar servidores DNS y extraer información valiosa. A continuación, se presentan algunas de las herramientas más populares y versátiles en el arsenal de los profesionales de reconocimiento web:

| **Herramienta**                        | **Características Clave**                                                                                                        | **Casos de Uso**                                                                                                                                                                 |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **dig**                                | Herramienta de búsqueda DNS versátil que soporta varios tipos de consultas (A, MX, NS, TXT, etc.) y salida detallada.            | Consultas DNS manuales, transferencias de zona (si están permitidas), resolución de problemas de DNS y análisis profundo de registros DNS.                                       |
| **nslookup**                           | Herramienta de búsqueda DNS más simple, principalmente para registros A, AAAA y MX.                                              | Consultas DNS básicas, verificaciones rápidas de la resolución de dominios y registros de servidores de correo.                                                                  |
| **host**                               | Herramienta de búsqueda DNS simplificada con salida concisa.                                                                     | Consultas rápidas de registros A, AAAA y MX.                                                                                                                                     |
| **dnsenum**                            | Herramienta de enumeración DNS automatizada, ataques de diccionario, fuerza bruta, transferencias de zona (si están permitidas). | Descubrimiento de subdominios y recopilación eficiente de información DNS.                                                                                                       |
| **fierce**                             | Herramienta de reconocimiento DNS y enumeración de subdominios con búsqueda recursiva y detección de comodines.                  | Interfaz amigable para el reconocimiento DNS, identificación de subdominios y posibles objetivos.                                                                                |
| **dnsrecon**                           | Combina múltiples técnicas de reconocimiento DNS y soporta varios formatos de salida.                                            | Enumeración DNS completa, identificación de subdominios y recopilación de registros DNS para análisis posterior.                                                                 |
| **theHarvester**                       | Herramienta OSINT que recopila información de varias fuentes, incluidos registros DNS (direcciones de correo electrónico).       | Recopilación de direcciones de correo electrónico, información de empleados y otros datos asociados con un dominio desde múltiples fuentes.                                      |
| **Servicios de Búsqueda DNS en Línea** | Interfaces amigables para realizar búsquedas DNS.                                                                                | Consultas DNS rápidas y fáciles, conveniente cuando las herramientas de línea de comandos no están disponibles, comprobación de disponibilidad de dominios o información básica. |

## **El Domain Information Groper**

El comando `dig` (Domain Information Groper) es una utilidad versátil y poderosa para consultar servidores DNS y recuperar varios tipos de registros DNS. Su flexibilidad y salida detallada y personalizable lo hacen una opción preferida.

### **Comandos Comunes de dig**

| **Comando**                     | **Descripción**                                                                                                                                                                  |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `dig domain.com`                | Realiza una búsqueda por defecto del registro A para el dominio.                                                                                                                 |
| `dig domain.com A`              | Recupera la dirección IPv4 (registro A) asociada con el dominio.                                                                                                                 |
| `dig domain.com AAAA`           | Recupera la dirección IPv6 (registro AAAA) asociada con el dominio.                                                                                                              |
| `dig domain.com MX`             | Encuentra los servidores de correo (registros MX) responsables del dominio.                                                                                                      |
| `dig domain.com NS`             | Identifica los servidores de nombres autoritativos para el dominio.                                                                                                              |
| `dig domain.com TXT`            | Recupera los registros TXT asociados con el dominio.                                                                                                                             |
| `dig domain.com CNAME`          | Recupera el registro de nombre canónico (CNAME) para el dominio.                                                                                                                 |
| `dig domain.com SOA`            | Recupera el registro de inicio de autoridad (SOA) para el dominio.                                                                                                               |
| `dig @1.1.1.1 domain.com`       | Especifica un servidor de nombres específico para consultar; en este caso, 1.1.1.1.                                                                                              |
| `dig +trace domain.com`         | Muestra el camino completo de la resolución DNS.                                                                                                                                 |
| `dig -x 192.168.1.1`            | Realiza una búsqueda inversa en la dirección IP 192.168.1.1 para encontrar el nombre de host asociado. Es posible que debas especificar un servidor de nombres.                  |
| `dig +short domain.com`         | Proporciona una respuesta corta y concisa a la consulta.                                                                                                                         |
| `dig +noall +answer domain.com` | Muestra solo la sección de respuesta de la salida de la consulta.                                                                                                                |
| `dig domain.com ANY`            | Recupera todos los registros DNS disponibles para el dominio (Nota: Muchos servidores DNS ignoran las consultas ANY para reducir la carga y prevenir abusos, según el RFC 8482). |

> **Precaución**: Algunos servidores pueden detectar y bloquear consultas DNS excesivas. Usa con cautela y respeta los límites de tasa. Siempre obtén permiso antes de realizar un reconocimiento DNS extensivo en un objetivo.

## Explorando DNS con `dig`

Ejecutar el comando `dig` para consultar el dominio `google.com` da como resultado:

```bash
sherlock28@htb[/htb]$ dig google.com

; <<>> DiG 9.18.24-0ubuntu0.22.04.1-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             0       IN      A       142.251.47.142

;; Query time: 0 msec
;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP)
;; WHEN: Thu Jun 13 10:45:58 SAST 2024
;; MSG SIZE  rcvd: 54
```

Este resultado de una consulta DNS usando el comando `dig` para el dominio `google.com` se puede desglosar en cuatro secciones clave:

### **1. Encabezado**

```yaml
yamlCopiar código;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available
```

* `opcode: QUERY`: Indica que se trata de una consulta.
* `status: NOERROR`: El estado es exitoso (sin errores).
* `id: 16449`: Identificador único para esta consulta.
* `flags: qr rd ad`: Describe las banderas en el encabezado DNS:
  * `qr`: Bandera de Respuesta a Consulta - indica que esta es una respuesta.
  * `rd`: Bandera de Recursión Deseada - significa que se solicitó recursión.
  * `ad`: Datos Auténticos - el resolvedor considera que los datos son auténticos.
* `QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0`: Indica el número de entradas en cada sección de la respuesta DNS.
* `WARNING: recursion requested but not available`: Indica que se solicitó recursión, pero el servidor no la soporta.

### **2. Sección de Consulta**

```css
;google.com.                    IN      A
```

* `google.com. IN A`: La pregunta es: "¿Cuál es la dirección IPv4 (registro A) para google.com?"

### **3. Sección de Respuesta**

```css
google.com.             0       IN      A       142.251.47.142
```

* `google.com. 0 IN A 142.251.47.142`: La respuesta a la consulta. Indica que la dirección IP asociada con `google.com` es `142.251.47.142`. El `0` representa el TTL (tiempo de vida), que indica cuánto tiempo se puede almacenar en caché el resultado antes de ser refrescado.

### **4. Pie de Página**

```yaml
;; Query time: 0 msec
;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP)
;; WHEN: Thu Jun 13 10:45:58 SAST 2024
;; MSG SIZE  rcvd: 54
```

* `Query time: 0 msec`: Muestra el tiempo que tardó la consulta en ser procesada y la respuesta en ser recibida (0 milisegundos).
* `SERVER: 172.23.176.1#53(172.23.176.1) (UDP)`: Identifica el servidor DNS que proporcionó la respuesta y el protocolo utilizado (UDP).
* `WHEN: Thu Jun 13 10:45:58 SAST 2024`: Marca de tiempo de cuándo se realizó la consulta.
* `MSG SIZE rcvd: 54`: Tamaño del mensaje DNS recibido (54 bytes).

**Consulta Rápida con `+short`**

Si solo deseas la respuesta a la consulta, sin la información adicional, puedes utilizar el modificador `+short`:

```bash
sherlock28@htb[/htb]$ dig +short hackthebox.com

104.18.20.126
104.18.21.126
```

Este comando proporciona una respuesta concisa, mostrando solo las direcciones IP asociadas con `hackthebox.com`.
