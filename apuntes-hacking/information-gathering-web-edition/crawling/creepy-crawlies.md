# Creepy Crawlies

**La exploración web** es vasta e intrincada, pero no tienes que emprender este viaje solo. Hay una gran cantidad de herramientas de rastreo web disponibles para ayudarte, cada una con sus propias fortalezas y especialidades. Estas herramientas automatizan el proceso de rastreo, haciéndolo más rápido y eficiente, permitiéndote enfocarte en analizar los datos extraídos.

## Herramientas Populares de Rastreo Web

* **Burp Suite Spider**: Burp Suite, una plataforma ampliamente utilizada para pruebas de aplicaciones web, incluye un potente rastreador activo llamado Spider. Spider destaca en la creación de mapas de aplicaciones web, la identificación de contenido oculto y la detección de vulnerabilidades potenciales.
* **OWASP ZAP (Zed Attack Proxy)**: ZAP es un escáner de seguridad de aplicaciones web gratuito y de código abierto. Puede usarse en modos automatizado y manual, e incluye un componente de rastreo para explorar aplicaciones web e identificar vulnerabilidades potenciales.
* **Scrapy (Framework de Python)**: Scrapy es un framework de Python versátil y escalable para construir rastreadores web personalizados. Ofrece características completas para extraer datos estructurados de sitios web, manejar escenarios de rastreo complejos y automatizar el procesamiento de datos. Su flexibilidad lo hace ideal para tareas de reconocimiento personalizadas.
* **Apache Nutch (Rastreador Escalable)**: Nutch es un rastreador web de código abierto altamente extensible y escalable escrito en Java. Está diseñado para manejar rastreos masivos a través de toda la web o centrarse en dominios específicos. Aunque requiere más experiencia técnica para configurarlo, su potencia y flexibilidad lo convierten en un recurso valioso para proyectos de reconocimiento a gran escala.

Adherirse a prácticas de rastreo éticas y responsables es crucial sin importar qué herramienta elijas. Siempre obtén permiso antes de rastrear un sitio web, especialmente si planeas realizar escaneos extensos o intrusivos. Ten en cuenta los recursos del servidor del sitio web y evita sobrecargarlos con solicitudes excesivas.

## Scrapy

Vamos a utilizar Scrapy y un rastreador personalizado diseñado para el reconocimiento en `inlanefreight.com`. Si estás interesado en más información sobre técnicas de rastreo/spidering, consulta el módulo "Uso de Proxies Web", ya que forma parte de CBBH también.

### **Instalación de Scrapy**

Antes de comenzar, asegúrate de tener Scrapy instalado en tu sistema. Si no lo tienes, puedes instalarlo fácilmente usando pip, el instalador de paquetes de Python:

```bash
pip3 install scrapy
```

Este comando descargará e instalará Scrapy junto con sus dependencias, preparando tu entorno para construir nuestro rastreador.

### **ReconSpider**

Primero, ejecuta este comando en tu terminal para descargar el rastreador Scrapy personalizado, ReconSpider, y extraerlo en el directorio de trabajo actual:

```bash
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
unzip ReconSpider.zip
```

Con los archivos extraídos, puedes ejecutar `ReconSpider.py` usando el siguiente comando:

```bash
python3 ReconSpider.py http://inlanefreight.com
```

Reemplaza `inlanefreight.com` con el dominio que deseas rastrear. El rastreador examinará el objetivo y recopilará información valiosa.

### **resultados.json**

Después de ejecutar `ReconSpider.py`, los datos se guardarán en un archivo JSON, `results.json`. Este archivo puede ser explorado utilizando cualquier editor de texto. A continuación se muestra la estructura del archivo JSON producido:

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}
```

Cada clave en el archivo JSON representa un tipo diferente de datos extraídos del sitio web objetivo:

* **emails**: Lista de direcciones de correo electrónico encontradas en el dominio.
* **links**: Lista de URLs de enlaces encontrados dentro del dominio.
* **external\_files**: Lista de URLs de archivos externos como PDFs.
* **js\_files**: Lista de URLs de archivos JavaScript utilizados por el sitio web.
* **form\_fields**: Lista de campos de formularios encontrados en el dominio (vacío en este ejemplo).
* **images**: Lista de URLs de imágenes encontradas en el dominio.
* **videos**: Lista de URLs de videos encontrados en el dominio (vacío en este ejemplo).
* **audio**: Lista de URLs de archivos de audio encontrados en el dominio (vacío en este ejemplo).
* **comments**: Lista de comentarios HTML encontrados en el código fuente.

Al explorar esta estructura JSON, puedes obtener valiosos conocimientos sobre la arquitectura, contenido y posibles puntos de interés para una investigación más profunda en la aplicación web.
