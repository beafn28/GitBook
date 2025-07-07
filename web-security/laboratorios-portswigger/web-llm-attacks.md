# Web LLM attacks

## Lab: Exploiting LLM APIs with excessive agency

### Enunciado

Para resolver el laboratorio, utiliza el LLM para eliminar al usuario **carlos**.

### Resolución

Como vemos es un asistente que puedes hablar en vivo. Tras muchas conversaciones por fin di en el clavo para eliminar al usuario **carlos**.

Atacar una integración LLM es similar a explotar un SSRF: se usa el sistema del servidor para atacar recursos internos.

**Cómo funcionan:**\
Los LLMs procesan entradas (prompts) y generan respuestas. En webs modernas pueden usarse para:

* Atención al cliente.
* Traducción.
* Análisis de contenido generado por usuarios.

**Problema común:**\
Los ataques suelen usar _prompt injection_, manipulando las respuestas del LLM para forzarlo a realizar acciones no previstas, como llamadas a APIs sensibles.

**Metodología para detectar vulnerabilidades:**

1. Identificar las entradas del LLM (directas e indirectas).
2. Determinar qué datos y APIs puede acceder.
3. Probar esa superficie de ataque.

**APIs del LLM:**\
El sitio puede describir APIs locales al LLM. Por ejemplo, un bot de soporte puede gestionar usuarios, pedidos o stock a través de sus APIs.

**Riesgo:**\
El LLM podría llamar a estas APIs en nombre del usuario sin que este lo sepa, si no hay confirmación previa.

**Excessive Agency:**\
Sucede cuando el LLM tiene acceso a APIs sensibles y puede ser manipulado para usarlas de forma insegura. El atacante explora preguntando qué APIs están disponibles o fingiendo ser un desarrollador.

**Exploración en el laboratorio:**

* Entrada directa: el prompt del chat en vivo.
* Entrada indirecta: datos de entrenamiento.
* Descubrimos que el LLM tiene acceso a un API interno que lista precios de productos.
* Confirmamos que también tiene acceso a funciones como `password_reset`, `debug_sql`, `product_info`.

**Explotación:**

* `password_reset` puede usarse para cambiar contraseñas de otras cuentas.
* `debug_sql` permite inyectar consultas SQL maliciosas.

**Ataque final:**\
Podemos pedirle al LLM que use la API `debug_sql` para ejecutar un comando que elimine al usuario carlos.

<figure><img src="../../.gitbook/assets/image (1533).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1534).png" alt=""><figcaption></figcaption></figure>

