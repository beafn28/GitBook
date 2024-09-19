# Search Engine Discovery

Los motores de búsqueda son nuestras guías en el vasto paisaje de internet, ayudándonos a navegar a través de la aparentemente interminable cantidad de información. Sin embargo, más allá de su función principal de responder consultas diarias, los motores de búsqueda también contienen una mina de datos que puede ser invaluable para la recopilación de información y el reconocimiento web. Esta práctica, conocida como descubrimiento mediante motores de búsqueda o recopilación de inteligencia de fuentes abiertas (OSINT), implica utilizar los motores de búsqueda como herramientas poderosas para descubrir información sobre sitios web objetivo, organizaciones e individuos.

## Importancia del Descubrimiento con Motores de Búsqueda

El descubrimiento con motores de búsqueda es un componente crucial del reconocimiento web por varias razones:

* **Fuente Abierta:** La información recopilada es accesible públicamente, lo que la convierte en una forma legal y ética de obtener información sobre un objetivo.
* **Amplitud de Información:** Los motores de búsqueda indexan una vasta porción de la web, ofreciendo una amplia gama de fuentes de información potenciales.
* **Facilidad de Uso:** Los motores de búsqueda son fáciles de usar y no requieren habilidades técnicas especializadas.
* **Costo-efectivo:** Es un recurso gratuito y fácilmente disponible para la recopilación de información.

La información que puedes obtener de los motores de búsqueda también puede aplicarse en varios ámbitos:

* **Evaluación de Seguridad:** Identificación de vulnerabilidades, datos expuestos y posibles vectores de ataque.
* **Inteligencia Competitiva:** Recolección de información sobre productos, servicios y estrategias de competidores.
* **Periodismo de Investigación:** Descubrimiento de conexiones ocultas, transacciones financieras y prácticas poco éticas.
* **Inteligencia de Amenazas:** Identificación de amenazas emergentes, seguimiento de actores maliciosos y predicción de posibles ataques.

Sin embargo, es importante tener en cuenta que el descubrimiento mediante motores de búsqueda tiene limitaciones. Los motores de búsqueda no indexan toda la información, y algunos datos pueden estar deliberadamente ocultos o protegidos.

## Operadores de Búsqueda

Los operadores de búsqueda son como códigos secretos de los motores de búsqueda. Estos comandos y modificadores especiales desbloquean un nuevo nivel de precisión y control, permitiéndote localizar tipos específicos de información en medio de la vastedad de la web indexada.

Aunque la sintaxis exacta puede variar ligeramente entre motores de búsqueda, los principios subyacentes permanecen consistentes. Vamos a explorar algunos operadores de búsqueda esenciales y avanzados:

| Operador              | Descripción del Operador                                                   | Ejemplo                                             | Descripción del Ejemplo                                                                               |
| --------------------- | -------------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `site:`               | Limita los resultados a un sitio web o dominio específico.                 | `site:example.com`                                  | Encuentra todas las páginas accesibles públicamente en example.com.                                   |
| `inurl:`              | Busca páginas con un término específico en la URL.                         | `inurl:login`                                       | Busca páginas de inicio de sesión en cualquier sitio web.                                             |
| `filetype:`           | Busca archivos de un tipo particular.                                      | `filetype:pdf`                                      | Encuentra documentos PDF descargables.                                                                |
| `intitle:`            | Busca páginas con un término específico en el título.                      | `intitle:"confidential report"`                     | Busca documentos titulados "confidential report" o variaciones similares.                             |
| `intext:` o `inbody:` | Busca un término dentro del texto de las páginas.                          | `intext:"password reset"`                           | Identifica páginas web que contienen el término "password reset".                                     |
| `cache:`              | Muestra la versión en caché de una página web (si está disponible).        | `cache:example.com`                                 | Visualiza la versión en caché de example.com para ver su contenido anterior.                          |
| `link:`               | Encuentra páginas que enlazan a una página web específica.                 | `link:example.com`                                  | Identifica sitios web que enlazan a example.com.                                                      |
| `related:`            | Encuentra sitios web relacionados con una página web específica.           | `related:example.com`                               | Descubre sitios web similares a example.com.                                                          |
| `info:`               | Proporciona un resumen de información sobre una página web.                | `info:example.com`                                  | Obtiene detalles básicos sobre example.com, como su título y descripción.                             |
| `define:`             | Proporciona definiciones de una palabra o frase.                           | `define:phishing`                                   | Obtiene una definición de "phishing" de varias fuentes.                                               |
| `numrange:`           | Busca números dentro de un rango específico.                               | `site:example.com numrange:1000-2000`               | Encuentra páginas en example.com que contienen números entre 1000 y 2000.                             |
| `allintext:`          | Busca páginas que contengan todas las palabras especificadas en el texto.  | `allintext:admin password reset`                    | Busca páginas que contengan tanto "admin" como "password reset" en el texto.                          |
| `allinurl:`           | Busca páginas que contengan todas las palabras especificadas en la URL.    | `allinurl:admin panel`                              | Busca páginas con "admin" y "panel" en la URL.                                                        |
| `allintitle:`         | Busca páginas que contengan todas las palabras especificadas en el título. | `allintitle:confidential report 2023`               | Busca páginas con "confidential," "report," y "2023" en el título.                                    |
| `AND`                 | Reduce los resultados requiriendo que todos los términos estén presentes.  | `site:example.com AND (inurl:admin OR inurl:login)` | Encuentra páginas de administrador o inicio de sesión en example.com.                                 |
| `OR`                  | Amplía los resultados incluyendo páginas con cualquiera de los términos.   | `"linux" OR "ubuntu" OR "debian"`                   | Busca páginas web que mencionen Linux, Ubuntu o Debian.                                               |
| `NOT`                 | Excluye resultados que contengan el término especificado.                  | `site:bank.com NOT inurl:login`                     | Encuentra páginas en bank.com excluyendo las páginas de inicio de sesión.                             |
| `*` (comodín)         | Representa cualquier carácter o palabra.                                   | `site:socialnetwork.com filetype:pdf user* manual`  | Busca manuales de usuario (guía del usuario, manual del usuario) en formato PDF en socialnetwork.com. |
| `..` (rango)          | Encuentra resultados dentro de un rango numérico especificado.             | `site:ecommerce.com "price" 100..500`               | Busca productos con precios entre 100 y 500 en un sitio de comercio electrónico.                      |
| `""` (comillas)       | Busca frases exactas.                                                      | `"information security policy"`                     | Encuentra documentos que mencionan la frase exacta "information security policy".                     |
| `-` (signo menos)     | Excluye términos de los resultados de búsqueda.                            | `site:news.com -inurl:sports`                       | Busca artículos de noticias en news.com excluyendo contenido relacionado con deportes.                |

## Google Dorking

Google Dorking, también conocido como Google Hacking, es una técnica que utiliza el poder de los operadores de búsqueda para descubrir información sensible, vulnerabilidades de seguridad o contenido oculto en sitios web, usando Google Search.

Aquí tienes algunos ejemplos comunes de Google Dorks. Para más ejemplos, consulta la Google Hacking Database:

* **Encontrar Páginas de Inicio de Sesión:**
  * `site:example.com inurl:login`
  * `site:example.com (inurl:login OR inurl:admin)`
* **Identificar Archivos Expuestos:**
  * `site:example.com filetype:pdf`
  * `site:example.com (filetype:xls OR filetype:docx)`
* **Descubrir Archivos de Configuración:**
  * `site:example.com inurl:config.php`
  * `site:example.com (ext:conf OR ext:cnf)`
* **Ubicar Copias de Seguridad de Bases de Datos:**
  * `site:example.com inurl:backup`
  * `site:example.com filetype:sql`
