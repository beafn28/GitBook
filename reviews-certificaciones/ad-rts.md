# AD-RTS

### Contexto&#x20;

He completado la certificación **Active Directory Red Team Specialist (AD-RTS)** de CyberWarFare Labs y, siendo directos, ha sido una de las formaciones que **mejor ha cerrado huecos conceptuales reales** que tenía sobre Active Directory.

No desde el punto de vista “qué comando ejecuto”, sino **por qué ciertas malas configuraciones acaban convirtiéndose en vulnerabilidades sistémicas dentro de un dominio**.

### Enfoque del curso

La certificación es **100% práctica**, orientada a **red teaming realista en entornos AD**, y se centra en:

* Entender **las raíces de las vulnerabilidades en Active Directory**
* Construir **attack paths completos**
* Simular ataques reales en un entorno corporativo&#x20;
* Trabajar con **AD, Exchange y ESXi** de forma integrada

### Contenido técnico aprendido

#### Fundamentos y contexto

Durante los materiales de estudio se cubren:

* Conceptos básicos de Active Directory
* Arquitectura y servicios críticos
* **Por qué existen ciertas vulnerabilidades** y qué decisiones de diseño las provocan
* Active Directory Certificate Services (ADCS) y sus superficies de ataque

Esto da contexto antes de empezar a explotar, algo que muchas formaciones se saltan.

### Dos caminos de explotación

Uno de los puntos más interesantes de la certificación es que se puede completar por **dos rutas distintas**, algo muy alineado con escenarios reales.

#### Authenticated Pathway

En el camino autenticado se trabajan ataques como:

* Explotación web sobre **IIS**
* Extracción de contraseñas desde el **Windows Registry**
* **Abuso de Active Directory Certificate Services**
* Acceso y abuso de **Exchange Server**

Aquí el foco está en **qué puede hacer un atacante una vez dentro**.

#### Unauthenticated Pathway

En el camino no autenticado se cubren:

* Enumeración DNS y **Zone Transfers**
* Enumeración LDAP
* **AS-REP Roasting**
* Explotación de **SQL Server**
* Credential dumping y exfiltración
* Abuso de **ADCS**
* Acceso a **ESXi Guest VMs**

### Qué se practica realmente

A lo largo del laboratorio se trabajan habilidades clave de red teaming:

* Enumeración estructurada
* Movimiento lateral
* Escalada de privilegios
* Persistencia
* Exfiltración de datos
* Operación contra:
  * Active Directory
  * Exchange
  * ESXi / virtualización

### Qué aporta esta certificación

* Cierra **lagunas conceptuales reales** sobre AD
* Conecta ataques que normalmente se ven aislados
* Refuerza el pensamiento en **attack chains**
* Enseña **cómo se mueve un atacante dentro de un dominio**
* Es útil tanto para red team como para blue team (para entender qué defender)

### Skills cubiertas

* Active Directory fundamentals & exploitation
* AD Certificate Services abuse
* Exchange attacks
* Lateral movement
* Privilege escalation & persistence
* ESXi red team operations
* Data exfiltration

### Nivel y perfil recomendado

Esta certificación es ideal si:

* Ya tienes base en AD o pentesting
* Quieres pasar de “sé ejecutar ataques” a “entiendo el impacto”
* Buscas **escenarios más cercanos a la realidad empresarial**

No es para empezar desde cero, pero **sí es perfecta para subir un escalón**.

### Conclusión

El **AD-RTS** es una certificación **muy bien planteada**, práctica y con sentido ofensivo real.

No se limita a enseñar técnicas sueltas: enseña **cómo y por qué los entornos Active Directory fallan**, y cómo un atacante puede aprovecharlo de principio a fin.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
