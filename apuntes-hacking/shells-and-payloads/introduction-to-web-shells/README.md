# Introduction to Web Shells

## Introducción a los Servidores Web y Web Shells en Pentesting

Es casi seguro que nos encontraremos con servidores web durante nuestro aprendizaje y práctica activa de pentesting. Gran parte de los servicios de software del mundo se están trasladando a plataformas basadas en la web, accesibles a través de la World Wide Web usando un navegador y HTTP/S. Solo considera el sitio web en el que estamos ahora. Está completamente en el navegador, accesible desde cualquier lugar del mundo utilizando cualquier dispositivo conectado a Internet. Los modernos medios de entretenimiento, como los videojuegos, la música y la transmisión de videos, son accesibles a través de navegadores y aplicaciones. Esto significa que nos encontraremos apuntando a aplicaciones web cada vez más a medida que pase el tiempo.

Además, durante las pruebas de penetración externas, a menudo encontramos que las redes perimetrales de los clientes están bien reforzadas. No exponen servicios vulnerables como SMB u otros elementos que solíamos encontrar con frecuencia. Estos elementos ahora los anticipamos principalmente durante una prueba de penetración interna. Durante nuestras pruebas de penetración externas, comúnmente "entramos" (obtenemos un pie dentro de la red interna) a través de ataques a aplicaciones web (ataques de carga de archivos, inyección SQL, RFI/LFI, inyección de comandos, etc.), pulverización de contraseñas (contra RDS, portales VPN, Citrix, OWA y otras aplicaciones que utilizan autenticación de Active Directory) y ingeniería social.

Las aplicaciones web son a menudo la mayoría de lo que vemos expuesto durante una evaluación de red externa y, a menudo, presentan una enorme superficie de ataque. Podemos encontrar formularios de carga de archivos públicamente disponibles que nos permiten subir directamente un web shell de PHP, JSP o ASP.NET. Algunas funcionalidades durante las pruebas autenticadas pueden estar presentes o, nuestro favorito personal, una funcionalidad de auto-registro donde podemos ingresar y subir un web shell (después de eludir las verificaciones del lado del cliente) en el área de carga de la foto de perfil del usuario. También podemos encontrar aplicaciones como Tomcat, Axis2 o WebLogic, que permiten desplegar código JSP a través de un archivo WAR como parte de su funcionalidad. Incluso podemos encontrar un servicio FTP mal configurado que permite cargas de archivos directamente en el directorio web del servidor. Hay muchas otras maneras en las que podemos encontrar para cargar un web shell que están fuera del alcance de este módulo.

## ¿Qué es un Web Shell?

Un web shell es una sesión de shell basada en el navegador que podemos usar para interactuar con el sistema operativo subyacente de un servidor web. Nuevamente, para obtener ejecución remota de código a través de un web shell, primero debemos encontrar una vulnerabilidad en un sitio web o aplicación web que nos dé capacidades de carga de archivos. La mayoría de los web shells se obtienen subiendo una carga útil escrita en un lenguaje web en el servidor objetivo. Las cargas útiles que subimos deben darnos capacidad de ejecución remota de código dentro del navegador. Las secciones y desafíos que siguen se centrarán principalmente en ejecutar comandos a través de nuestros web shells en el navegador. Sin embargo, es esencial saber que confiar solo en el web shell para interactuar con el sistema puede ser inestable e poco fiable porque algunas aplicaciones web están configuradas para eliminar cargas de archivos después de un cierto período de tiempo. Para lograr persistencia en un sistema, en muchos casos, esta es la forma inicial de obtener ejecución remota de código a través de una aplicación web, que luego podemos usar para actualizar a un shell inverso más interactivo.

En las próximas secciones, aprenderemos y experimentaremos con varios web shells que nos permiten interactuar con el sistema operativo subyacente de un servidor web a través del navegador.