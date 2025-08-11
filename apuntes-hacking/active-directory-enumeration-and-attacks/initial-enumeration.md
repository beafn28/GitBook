# Initial Enumeration

## Principios de Reconocimiento y Enumeración Externa

Antes de comenzar cualquier **pentest**, resulta beneficioso realizar **reconocimiento externo** del objetivo. Esto sirve para:

* **Validar información** proporcionada en el documento de alcance del cliente.
* **Asegurar** que estamos actuando contra los sistemas correctos cuando trabajamos en remoto.
* **Descubrir información pública** que pueda impactar en la prueba (como credenciales filtradas).

> En otras palabras: queremos **entender el terreno** para ofrecer la prueba más completa posible, identificando filtraciones o datos de brechas expuestos al mundo.

### ¿Qué buscamos?

Durante el reconocimiento externo, queremos identificar información clave. Aunque no siempre esté disponible públicamente, vale la pena intentar encontrarla. Si nos quedamos atascados en un pentest, revisar la información recolectada pasivamente puede darnos **el empujón necesario para avanzar**.

| **Elemento**                    | **Descripción**                                                                                                                                                  |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Espacio IP**                  | ASN válido, bloques de red públicos, presencia en la nube, proveedores de hosting, registros DNS.                                                                |
| **Información de dominio**      | DNS, registros del sitio, subdominios, servicios accesibles (correo, VPN, portales), indicios de defensas (SIEM, AV, IDS/IPS).                                   |
| **Formato de nombres/usuarios** | Emails corporativos, convenciones de nombres en AD, políticas de contraseñas. Base para listas de usuarios para **password spraying** o **credential stuffing**. |
| **Divulgación de datos**        | Archivos públicos (.pdf, .ppt, .docx, .xlsx) con datos sensibles: links de intranet, metadatos de usuarios, shares, credenciales expuestas en GitHub, etc.       |
| **Datos de brechas**            | Credenciales filtradas públicamente que podrían ser usadas para obtener acceso inicial.                                                                          |

***

### ¿Dónde buscamos?

Existen muchísimas fuentes y herramientas para recolectar esta información. Aquí algunos recursos comunes:

| **Recurso**                    | **Ejemplos**                                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| ASN / IP registrars            | IANA, ARIN (Américas), RIPE (Europa), BGP Toolkit.                                                           |
| Registradores de dominio y DNS | Domaintools, PTRArchive, ICANN, consultas DNS manuales (8.8.8.8).                                            |
| Redes sociales                 | LinkedIn, Twitter, Facebook, artículos de prensa, cualquier info pública sobre la organización.              |
| Sitios web corporativos        | Noticias, páginas "Sobre Nosotros" o "Contacto", documentos incrustados.                                     |
| Espacios en la nube y Dev      | GitHub, buckets S3 de AWS, Azure Blob Storage, Google Dorks.                                                 |
| Breach Data Sources            | HaveIBeenPwned, Dehashed para emails corporativos, contraseñas en texto plano o hashes para crackeo offline. |

***

### IP/ASN y Address Spaces

**BGP-Toolkit de Hurricane Electric** es ideal para investigar bloques de direcciones asignados y su ASN.

Solo con un dominio o IP → obtenemos:

* Bloques de direcciones del objetivo.
* ASN propietario o compartido.
* Clues sobre **auto-hosting vs. cloud providers**.

> **Cuidado:** organizaciones pequeñas suelen compartir hosting. Si no verificamos el alcance, podríamos afectar a un tercero.

**Best practice:** Confirmar en el documento de alcance si se permite atacar infraestructura en la nube o alojada por terceros.

### DNS

Sirve para **validar alcance** y descubrir hosts:

* Domaintools, ViewDNS.info → Reverse IP, Whois, MX, NS, etc.
* Descubrimiento de subdominios no listados en el alcance.
* Validación cruzada con resultados de IP/ASN.

> Si aparecen hosts potencialmente fuera de alcance, discutidlo con el cliente antes de proceder.

### Información Pública

Redes sociales → **joya para recon**:

* Estructura organizacional.
* Equipamiento o software usado.
* Publicaciones de empleo → versiones de software, planes de migración.

**Ejemplo:** Oferta de SharePoint Admin → descubrimos versiones (2013, 2016) → posibles vectores de ataque si existen instalaciones desactualizadas.

Sitios web corporativos:

* Emails, teléfonos, organigramas.
* Documentos con metadatos → nombres de usuarios AD, hosts internos, enlaces a intranet.
* Fugas en repositorios GitHub, S3, etc.

> Herramientas útiles: **TruffleHog, GreyhatWarfare.**

### Ejemplo de Address Space Enumeration

```
IP Address: 134.209.24.248
Mail Server: mail1.inlanefreight.com
Nameservers: ns1.inlanefreight.com, ns2.inlanefreight.com
```

Validación con viewdns.info:

```bash
nslookup ns1.inlanefreight.com
→ 178.128.39.165

nslookup ns2.inlanefreight.com
→ 206.189.119.186
```

→ **Nuevos IPs potenciales para validar alcance.**

### Hunting for Files

Google Dork:

```
filetype:pdf inurl:inlanefreight.com
```

Resultado:

* Documento: _corporate goals and strategy_.
* Guardar localmente para análisis → buscar metadatos, nombres de usuarios, referencias a infraestructura interna.

### Hunting for Email Addresses

Google Dork:

```
intext:"@inlanefreight.com" inurl:inlanefreight.com
```

→ Página de Contacto:

* Lista de empleados activos.
* Esquema de email: `first.last@inlanefreight.com`.

> Información crucial para password spraying o ingeniería social.

Tools:

* **linkedin2username** para generar listas de usuarios.

### Credential Hunting

Ejemplo con **Dehashed**:

```bash
python3 dehashed.py -q inlanefreight.local -p
```

Resultados ficticios:

```
email: roger.grimes@inlanefreight.local
username: rgrimes
password: Ilovefishing!
```

Usos:

* Generar diccionarios para password spraying.
* Intentar credenciales en portales expuestos (VPN, OWA, Citrix, etc.).

> Credenciales antiguas → muchas veces inservibles, pero algunas funcionan.

### Principios Generales de Enumeración

Objetivo: **entender el objetivo** → descubrir rutas hacia el interior.\
Proceso **iterativo**:

* Enumeración pasiva → amplia.
* Filtrado de resultados.
* Enumeración activa → validación y profundización.

**Proceso ejemplo:**

1️- Passive Recon (OSINT).\
2- DNS y IP validation.\
3️- File hunting y email harvesting.\
4️- Breach data hunting.\
5️- Crear diccionarios personalizados.\
6️- Validar accesos externos.\
7️- Planificar ataque interno si se consiguen credenciales.

### Buenas Prácticas

Mantén un registro detallado:

* Archivos, capturas, salidas de herramientas.
* Para **evidencia y reporting**.

Asegúrate del **alcance autorizado**:

* No ataques infraestructura fuera de alcance.
* Si hay dudas → clarifica con el cliente.

Aprovecha la info recolectada:

* Genera listas de usuarios para ataques dirigidos.
* Planea password spraying estratégico.

> **Consejo:** la mayoría de la enumeración interna puede hacerse con **una cuenta de usuario estándar**.
