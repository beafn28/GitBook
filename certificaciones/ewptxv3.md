# eWPTXv3

## Review de mi preparación

Mi preparación se centró **exclusivamente en práctica técnica y análisis ofensivo realista**, apoyándome en módulos específicos de **Hack The Box (HTB)** y en laboratorios de **PortSwigger Web Security Academy**, evitando deliberadamente cursos excesivamente densos o excesivamente teóricos (INE y similares).

### Módulos trabajados de HTB

* API Attacks
* Web Service & API Attacks
* Attacking GraphQL
* Attacking Authentication Mechanisms
* SQL Injection Fundamentals
* SQLMap Essentials
* Introduction to NoSQL Injection
* Advanced SQL Injections
* Blind SQL Injection

### Módulos de PortSwigger

| Módulo                               | Prioridad   | Nota                                                                            |
| ------------------------------------ | ----------- | ------------------------------------------------------------------------------- |
| SQL injection                        | ✅ Sí        | Fundamental para el 15% de Injections.                                          |
| Cross-site scripting                 | ⚠️ Opcional | No está explícito en el temario. Útil para recon y fuzzing pero no prioritario. |
| Cross-site request forgery (CSRF)    | ✅ Sí        | Útil para metodología general.                                                  |
| Clickjacking                         | ❌ No        | Nada que ver con los objetivos del examen.                                      |
| DOM-based vulnerabilities            | ⚠️ Opcional | Ayuda para fuzzing y recon, pero no es core.                                    |
| Cross-origin resource sharing (CORS) | ⚠️ Opcional | Bueno para recon y comprensión, pero no core.                                   |
| XML external entity (XXE) injection  | ✅ Sí        | Está en el temario (Server-Side Attacks, Filter Evasion).                       |
| Server-side request forgery (SSRF)   | ✅ Sí        | Explicitamente en Server-Side Attacks y Filter Evasion.                         |
| HTTP request smuggling               | ⚠️ Opcional | Útil para metodología avanzada, pero no listado en el temario.                  |
| OS command injection                 | ✅ Sí        | Está en Server-Side Attacks e Injection Vulnerabilities.                        |
| Server-side template injection       | ✅ Sí        | Directamente mapeado a Server-Side Attacks e Injection.                         |
| Path traversal                       | ⚠️ Opcional | Ayuda para fuzzing / recon pero no mencionado directamente.                     |
| Access control vulnerabilities       | ⚠️ Opcional | Útil para lógica de negocio y recon, pero no prioritario.                       |
| Authentication                       | ✅ Sí        | Clave para Authentication Attacks y API Penetration Testing.                    |
| WebSockets                           | ⚠️ Opcional | Interesante para recon, pero no listado en temario.                             |
| Web cache poisoning                  | ❌ No        | No aplica para el temario dado.                                                 |
| Insecure deserialization             | ✅ Sí        | Está en Server-Side Attacks explícitamente.                                     |
| Information disclosure               | ⚠️ Opcional | Útil para Recon, pero no core.                                                  |
| Business logic vulnerabilities       | ✅ Sí        | Imprescindible para API testing, recon y metodología.                           |
| HTTP Host header attacks             | ⚠️ Opcional | No en temario directo pero vale para fuzzing y recon.                           |
| OAuth authentication                 | ✅ Sí        | Clave en Authentication Attacks y API Pen Testing.                              |
| File upload vulnerabilities          | ⚠️ Opcional | Puede ser útil para RCE/Injection pero no se destaca en el temario.             |
| JWT                                  | ✅ Sí        | Específico en Authentication y API Penetration Testing.                         |
| Essential skills                     | ✅ Sí        | Obligatorio para "Methodology" y el 10% dedicado a ello.                        |
| Prototype pollution                  | ⚠️ Opcional | No aparece en el temario.                                                       |
| GraphQL API vulnerabilities          | ✅ Sí        | Muy relevante para el 25% de API Pen Testing.                                   |
| Race conditions                      | ⚠️ Opcional | Interesante para lógica de negocio pero no prioritario.                         |
| NoSQL injection                      | ✅ Sí        | Directamente en Injection Vulnerabilities.                                      |
| API testing                          | ✅ Sí        | El 25% del temario. Obligatorio.                                                |
| Web LLM attacks                      | ❌ No        | No tiene nada que ver con el temario.                                           |

### Alcance técnico cubierto

#### Autenticación y sesiones

* Broken Authentication
* Bypass de autenticación
* Cracking y manipulación de JWT
  * Algoritmos débiles
  * `alg: none`
  * Key confusion
  * Expiraciones mal gestionadas
* Mala gestión de sesiones en APIs
  * Tokens reutilizables
  * Ausencia de rotación
  * Expiraciones incorrectas

#### Inyecciones

* Inyección SQL
* Inyección NoSQL
* Automatización y explotación con SQLMap
* Deserialización insegura

#### APIs y servicios web

* Enumeración y explotación de APIs REST mal configuradas
* Bypass de controles de acceso (403)
* Information Leakage a nivel de CVE
* Fallos lógicos en APIs
* Ataques específicos a GraphQL
  * Introspection
  * Over-fetching
  * Authorization bypass

#### JWT y control de acceso

* Ataques comunes contra JWT
* Manipulación de claims
* Escalada de privilegios por validaciones defectuosas

### Recursos utilizados

* [https://dev-angelist.gitbook.io/ewptxv3-notes](https://dev-angelist.gitbook.io/ewptxv3-notes)
* [https://dev-angelist.gitbook.io/ewptxv3-notes/roadmap-exam-preparation](https://dev-angelist.gitbook.io/ewptxv3-notes/roadmap-exam-preparation)

### Enfoque de la preparación

La preparación fue **100% práctica**, orientada a **entornos reales**, priorizando explotación efectiva frente a memorización de contenido teórico.

No se basó en seguir cursos largos y densos, sino en **laboratorios, escenarios vulnerables reales y análisis técnico**, permitiendo una comprensión sólida de **cómo y por qué fallan las aplicaciones web y las APIs modernas**.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-12-23 a las 14.34.28.png" alt=""><figcaption></figcaption></figure>
