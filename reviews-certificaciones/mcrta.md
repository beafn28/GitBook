# MCRTA

### Contexto y motivación

El **MCRTA (Multi-Cloud Red Team Analyst)** de **CyberWarFare Labs** lo hice por una razón muy concreta: **aprender cloud hacking** sin tener que montar infraestructuras reales ni quemar dinero en suscripciones.\
Por **precio y planteamiento**, era una oportunidad clara para **meter mano a AWS, Azure y GCP desde el punto de vista ofensivo.**

### Qué es MCRTA

* Certificación **introductoria** en cloud red teaming
* Enfoque **100% práctico**
* Multi-cloud real:
  * AWS
  * Azure
  * GCP
* Formato **CTF / flags**

### Contenido y estructura

El curso está dividido por proveedor, con un patrón claro y repetible (algo que se agradece):

#### AWS

* Enumeración de S3
* Abuso de **SSRF → EC2 Metadata**
* Obtención de credenciales temporales
* Enumeración de IAM:
  * users
  * groups
  * roles
  * policies
* Abuso de relaciones de confianza (AssumeRole)
* Acceso a información sensible almacenada en buckets

#### Azure

* SSRF contra **Azure Instance Metadata Service**
* Tokens para:
  * Microsoft Graph
  * Azure Resource Manager
* Enumeración de:
  * Entra ID
  * subscriptions
  * roles
  * custom roles
* Abuso de permisos a nivel:
  * tenant
  * subscription
  * resource group
  * VM
* Enumeración de aplicaciones, owners y permisos Graph

#### GCP

* OSINT y filtrado de repositorios
* Abuso de **service account keys expuestas**
* Autenticación con `gcloud`
* Enumeración de:
  * proyectos
  * instancias
  * IAM roles
* SSRF para obtener tokens de service accounts
* Acceso a Cloud Storage y exfiltración de secretos

Todo lo anterior se refuerza con **laboratorios reales.**

### Conclusión

El **MCRTA vale claramente más de lo que cuesta**.

Si te interesa el cloud desde el punto de vista ofensivo, **este es un muy buen primer paso**.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
