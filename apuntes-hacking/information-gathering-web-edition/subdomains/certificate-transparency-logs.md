# Certificate Transparency Logs

Los registros de **Transparencia de Certificados** (CT) son registros públicos y de solo adición que documentan la emisión de certificados SSL/TLS. Cada vez que una Autoridad de Certificación (CA) emite un nuevo certificado, debe enviarlo a varios registros CT. Estas entidades independientes mantienen los registros y están abiertas para que cualquier persona los inspeccione.

Piensa en los registros CT como un registro global de certificados. Proporcionan un registro transparente y verificable de cada certificado SSL/TLS emitido para un sitio web. Esta transparencia sirve para varios propósitos cruciales:

* **Detección Temprana de Certificados Fraudulentos**: Al monitorear los registros CT, los investigadores de seguridad y los propietarios de sitios web pueden identificar rápidamente certificados sospechosos o mal emitidos. Un certificado fraudulento es un certificado digital no autorizado o falso emitido por una autoridad de certificación confiable. Detectarlos a tiempo permite tomar medidas rápidas para revocar los certificados antes de que puedan ser utilizados con fines maliciosos.
* **Responsabilidad de las Autoridades de Certificación**: Los registros CT responsabilizan a las CA por sus prácticas de emisión. Si una CA emite un certificado que viola las reglas o estándares, será visible públicamente en los registros, lo que puede llevar a sanciones o pérdida de confianza.
* **Fortalecimiento de la Infraestructura de Clave Pública (PKI) de la Web**: La PKI de la Web es el sistema de confianza que sustenta la comunicación en línea segura. Los registros CT ayudan a mejorar la seguridad y la integridad de la PKI de la Web proporcionando un mecanismo para la supervisión pública y la verificación de certificados.

## Cómo Funcionan los Registros CT

### CT Logs y Reconocimiento Web

Los registros de Transparencia de Certificados ofrecen una ventaja única en la enumeración de subdominios en comparación con otros métodos. A diferencia de los enfoques de fuerza bruta o basados en listas de palabras, que dependen de adivinar o predecir nombres de subdominios, los registros CT proporcionan un registro definitivo de certificados emitidos para un dominio y sus subdominios. Esto significa que no estás limitado por el alcance de tu lista de palabras o la efectividad de tu algoritmo de fuerza bruta. En su lugar, obtienes una vista histórica y completa de los subdominios de un dominio, incluidos aquellos que pueden no estar activos o ser fácilmente adivinables.

Además, los registros CT pueden revelar subdominios asociados con certificados antiguos o vencidos. Estos subdominios podrían alojar software o configuraciones desactualizadas, lo que los hace potencialmente vulnerables a la explotación.

En esencia, los registros CT proporcionan una manera confiable y eficiente de descubrir subdominios sin la necesidad de una fuerza bruta exhaustiva o depender de la integridad de las listas de palabras. Ofrecen una ventana única al historial de un dominio y pueden revelar subdominios que de otro modo podrían permanecer ocultos, mejorando significativamente tus capacidades de reconocimiento.

### Buscar en Registros CT

Hay dos opciones populares para buscar en los registros CT:

#### crt.sh

* **Características Clave**: Interfaz web amigable, búsqueda simple por dominio, muestra detalles del certificado, entradas SAN.
* **Casos de Uso**: Búsquedas rápidas y fáciles, identificación de subdominios, verificación del historial de emisión de certificados.
* **Ventajas**: Gratis, fácil de usar, no requiere registro.
* **Desventajas**: Opciones limitadas de filtrado y análisis.

#### Censys

* **Características Clave**: Potente motor de búsqueda para dispositivos conectados a Internet, filtrado avanzado por dominio, IP, atributos del certificado.
* **Casos de Uso**: Análisis en profundidad de certificados, identificación de malas configuraciones, búsqueda de certificados y hosts relacionados.
* **Ventajas**: Datos extensos y opciones de filtrado, acceso a API.
* **Desventajas**: Requiere registro (disponible un nivel gratuito).

### Ejemplo de Búsqueda en crt.sh

Mientras que crt.sh ofrece una interfaz web conveniente, también puedes aprovechar su API para búsquedas automatizadas directamente desde tu terminal. Aquí te mostramos cómo encontrar todos los subdominios con el prefijo "dev" en facebook.com usando `curl` y `jq`:

```bash
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u
```

* `curl -s "https://crt.sh/?q=facebook.com&output=json"`: Este comando obtiene la salida en JSON de crt.sh para los certificados que coinciden con el dominio facebook.com.
* `jq -r '.[] | select(.name_value | contains("dev")) | .name_value'`: Esta parte filtra los resultados JSON, seleccionando solo las entradas donde el campo `name_value` (que contiene el dominio o subdominio) incluye la cadena "dev". La opción `-r` indica a jq que devuelva cadenas sin formato.
* `sort -u`: Ordena los resultados alfabéticamente y elimina duplicados.
