# eCPPTv3

Esta sección complementa la preparación previa, enfocándose en **Active Directory, entornos Windows y post-explotación**, con un enfoque práctico y realista, orientado a escenarios típicos de certificaciones y entornos corporativos reales.

### Técnicas y vectores trabajados en Active Directory

Durante la preparación se practicaron de forma activa los siguientes ataques y técnicas:

* **Password Spraying** contra usuarios del dominio
* **AS-REP Roasting** y cracking de hashes Kerberos
* **Movimiento lateral** mediante:
  * SMB
  * WinRM
* **Dumping de hashes y credenciales** usando herramientas como:
  * `mimikatz`
  * `impacket`
* **Abuso de configuraciones débiles** en:
  * Domain Controllers
  * File Servers
* Enumeración avanzada de AD:
  * Usuarios
  * Grupos
  * GPOs
  * Delegaciones y permisos incorrectos

### Enfoque general de la preparación

El enfoque fue **100% práctico**, priorizando:

* Enumeración completa antes de explotar
* Automatización cuando aporta valor
* Comprensión del **flujo lógico de compromiso en AD**, no solo la ejecución de herramientas

No se utilizó el material oficial de INE como base principal, ya que resulta innecesariamente denso para este tipo de objetivos.\
La preparación se apoyó principalmente en **Hack The Box Academy**, máquinas de laboratorio y plataformas alternativas.

### Módulos trabajados (Hack The Box Academy)

* **Active Directory Enumeration & Attacks**\
  [https://academy.hackthebox.com/module/details/143](https://academy.hackthebox.com/module/details/143)
* **Introduction to Active Directory**\
  [https://academy.hackthebox.com/module/details/74](https://academy.hackthebox.com/module/details/74)

Estos módulos cubren de forma sólida:

* Enumeración de AD
* Kerberos abuse
* Lateral movement
* Privilege escalation en Windows
* Abuso de servicios mal configurados

### Máquinas Hack The Box recomendadas

Forest, Sauna, Active, Blackfield, Resolute, Reel, Sizzle, Mantis, Cascade, Multimaster

### Plataformas alternativas recomendadas (AD)

Además de HTB, se recomienda **encarecidamente** practicar Active Directory en:

* The Hacker Labs
* Vulnyx

Estas plataformas ayudan a **romper la dependencia exclusiva de HTB.**

### Recomendaciones prácticas

* Enumera **todo** antes de explotar: usuarios, servicios, shares, políticas
*   Si hay diccionarios proporcionados, **úsalos primero**

    > No es una trampa: si están ahí, la credencial suele estar ahí
* Ejecuta ataques de diccionario **en paralelo** para optimizar tiempo
* Si te atascas demasiado tiempo en un vector, cambia de enfoque
* Aunque el foco sea AD, **la explotación web sigue siendo clave**
* Debes saber:
  * Escalar privilegios en Linux
  * Pivotar desde web a Windows
  * Elevar privilegios una vez dentro de un host Windows

El acceso al AD rara vez es directo. Saber **qué hacer después de la primera shell** marca la diferencia entre avanzar o quedarse bloqueado.

### Conclusión

Esta preparación permite desarrollar habilidades reales en:

* Explotación de Active Directory
* Movimiento lateral en entornos Windows
* Elevación de privilegios
* Integración Web → Windows → AD

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-12-23 a las 18.20.19.png" alt=""><figcaption></figcaption></figure>
