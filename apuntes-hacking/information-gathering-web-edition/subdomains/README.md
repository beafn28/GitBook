# Subdomains

Al explorar los registros DNS, generalmente nos enfocamos en el dominio principal (por ejemplo, example.com) y su información asociada. Sin embargo, bajo la superficie de este dominio principal se encuentra una red potencial de subdominios. Estos subdominios son extensiones del dominio principal y a menudo se crean para organizar y separar diferentes secciones o funcionalidades de un sitio web. Por ejemplo, una empresa podría usar **blog.example.com** para su blog, **shop.example.com** para su tienda en línea o **mail.example.com** para sus servicios de correo electrónico.

## ¿Por qué es importante para el reconocimiento web?

Los subdominios a menudo alojan información y recursos valiosos que no están vinculados directamente desde el sitio web principal. Esto puede incluir:

* **Entornos de desarrollo y pruebas:** Las empresas suelen usar subdominios para probar nuevas funciones o actualizaciones antes de implementarlas en el sitio principal. Debido a medidas de seguridad relajadas, estos entornos pueden contener vulnerabilidades o exponer información sensible.
* **Portales de inicio de sesión ocultos:** Los subdominios pueden albergar paneles administrativos u otras páginas de inicio de sesión que no están destinadas a ser accesibles públicamente. Los atacantes que buscan acceso no autorizado pueden considerar estos como objetivos atractivos.
* **Aplicaciones heredadas:** Aplicaciones web antiguas o desactualizadas pueden residir en subdominios, y podrían contener software con vulnerabilidades conocidas.
* **Información sensible:** Los subdominios pueden exponer inadvertidamente documentos confidenciales, datos internos o archivos de configuración que podrían ser valiosos para los atacantes.

## Enumeración de subdominios

La enumeración de subdominios es el proceso de identificar y listar sistemáticamente estos subdominios. Desde una perspectiva DNS, los subdominios suelen estar representados por registros **A** (o **AAAA** para IPv6), que asignan el nombre del subdominio a su dirección IP correspondiente. Además, los registros **CNAME** pueden usarse para crear alias de subdominios, apuntándolos a otros dominios o subdominios.

Existen dos enfoques principales para la enumeración de subdominios:

### **1. Enumeración activa de subdominios**

Este enfoque implica interactuar directamente con los servidores DNS del dominio objetivo para descubrir subdominios. Un método es intentar una **transferencia de zona DNS**, donde un servidor mal configurado podría filtrar inadvertidamente una lista completa de subdominios. Sin embargo, debido a medidas de seguridad más estrictas, esto rara vez tiene éxito.

Una técnica activa más común es la **enumeración por fuerza bruta**, que consiste en probar sistemáticamente una lista de posibles nombres de subdominios contra el dominio objetivo. Herramientas como **dnsenum**, **ffuf** y **gobuster** pueden automatizar este proceso, utilizando listas de palabras con nombres de subdominios comunes o listas personalizadas basadas en patrones específicos.

### **2. Enumeración pasiva de subdominios**

Este enfoque se basa en fuentes externas de información para descubrir subdominios sin consultar directamente los servidores DNS del objetivo. Un recurso valioso son los **logs de Certificados de Transparencia (CT)**, repositorios públicos de certificados SSL/TLS. Estos certificados a menudo incluyen una lista de subdominios asociados en su campo **Subject Alternative Name (SAN)**, proporcionando una valiosa fuente de posibles objetivos.

Otro enfoque pasivo consiste en utilizar motores de búsqueda como Google o DuckDuckGo. Al emplear operadores de búsqueda especializados (por ejemplo, **site:**), se pueden filtrar los resultados para mostrar solo subdominios relacionados con el dominio objetivo.

Además, diversas bases de datos y herramientas en línea agregan datos DNS de múltiples fuentes, lo que permite buscar subdominios sin interactuar directamente con el objetivo.

Cada uno de estos métodos tiene sus ventajas y desventajas. La enumeración activa ofrece más control y la posibilidad de un descubrimiento más completo, pero puede ser más detectable. La enumeración pasiva es más sigilosa, pero podría no descubrir todos los subdominios existentes. Combinar ambos enfoques proporciona una enumeración más completa y efectiva de subdominios.
