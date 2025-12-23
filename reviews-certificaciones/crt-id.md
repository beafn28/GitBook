# CRT-ID

La **CRT-ID** de CyberWarFare Labs ataca un problema muy concreto dentro del red teaming:\
**la mayoría sabe explotar, pocos saben montar infraestructura sin dejarla expuesta**.

Esta certificación no va de payloads “cool” ni de TTPs post-explotación, va de algo mucho menos vistoso pero infinitamente más crítico: **infraestructura, canalización y OPSEC**.

### Qué aporta realmente

CRT-ID no intenta ser un programa gigantesco. Es más bien un **bloque fundacional** que te enseña a:

* levantar un C2 funcional
* esconderlo razonablemente bien
* canalizar tráfico sin delatarte
* y entender por qué se usan redirectores y CDNs

Si nunca has montado una infra completa de red team, este es tu momento.

### Contenido trabajado

Durante el curso se trabaja con un enfoque claramente operativo:

* **C2 moderno**: Mythic + Apollo, perfiles HTTP/HTTPS, roles de operador y hardening básico.
* **Canalización y OPSEC**:
  * Redirectores con Nginx
  * Filtros por User-Agent, IP y rutas
  * Uso de “facade files”
* **Infra en cloud**:
  * CloudFront
  * Azure Front Door usados como capa de protección real del C2.
* **Payload delivery**:
  * Hosting controlado con PwnDrop
  * Separación clara entre C2 y artefactos
* **Phishing operativo**:
  * Evilginx + GoPhish
  * Lures realistas (LNK, JS, ISO)
  * Cadena completa hasta inyección de payload

### El examen

El examen es **práctico** y relativamente corto. No mide memoria, mide si **sabes montar y operar**.

No es un “despliega y ya”, sino:

* comprobar que entiendes el porqué de cada componente
* validar que sabes encadenarlos sin romper OPSEC

Si has replicado y entendido los módulos, el examen es fácil. Es más largo desplegar la infra que las propias preguntas en sí.

### Nivel real

* Intermedio
* No es para principiantes absolutos
* Tampoco es una formación avanzada de emulación completa

### Conclusión

Pretende que **no te pillen por errores básicos de infraestructura**.

Como punto de entrada a infra ofensiva moderna:

* cumple
* enseña lo correcto
* y evita malas prácticas comunes

Una certificación pequeña, práctica y bien enfocada. Ideal para pasar de la teoría a **infra real**.

<figure><img src="../.gitbook/assets/image (1721).png" alt=""><figcaption></figcaption></figure>
