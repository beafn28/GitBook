# MySQL

MySQL es un sistema de gestión de bases de datos relacional SQL de código abierto, desarrollado y soportado por Oracle. Una base de datos es simplemente una colección estructurada de datos organizada para un uso y recuperación fáciles. El sistema de base de datos puede procesar grandes cantidades de datos con alta eficiencia. Dentro de la base de datos, el almacenamiento de datos se realiza de manera que ocupe el menor espacio posible. La base de datos se controla utilizando el lenguaje de base de datos SQL. MySQL funciona según el principio cliente-servidor y consta de un servidor MySQL y uno o más clientes MySQL. El servidor MySQL es el sistema de gestión de base de datos real. Se encarga del almacenamiento y distribución de datos. Los datos se almacenan en tablas con diferentes columnas, filas y tipos de datos. Estas bases de datos a menudo se almacenan en un solo archivo con la extensión .sql, por ejemplo, como `wordpress.sql`.

### Clientes MySQL

Los clientes MySQL pueden recuperar y editar los datos utilizando consultas estructuradas al motor de base de datos. Insertar, eliminar, modificar y recuperar datos se realiza utilizando el lenguaje de base de datos SQL. Por lo tanto, MySQL es adecuado para gestionar muchas bases de datos diferentes a las que los clientes pueden enviar múltiples consultas simultáneamente. Dependiendo del uso de la base de datos, el acceso es posible a través de una red interna o de Internet público.

Uno de los mejores ejemplos de uso de bases de datos es el CMS WordPress. WordPress almacena todas las publicaciones creadas, nombres de usuario y contraseñas en su propia base de datos, que solo es accesible desde el localhost. Sin embargo, como se explica con más detalle en el módulo Introducción a las Aplicaciones Web, existen estructuras de bases de datos distribuidas en múltiples servidores también.

### Bases de Datos MySQL

MySQL es ideal para aplicaciones como sitios web dinámicos, donde la sintaxis eficiente y la alta velocidad de respuesta son esenciales. A menudo se combina con un sistema operativo Linux, PHP y un servidor web Apache y también se conoce en esta combinación como LAMP (Linux, Apache, MySQL, PHP), o cuando se utiliza Nginx, como LEMP. En un alojamiento web con base de datos MySQL, esto sirve como una instancia central en la que se almacena el contenido requerido por los scripts PHP. Entre estos se encuentran:

* Encabezados
* Textos
* Etiquetas meta
* Formularios
* Clientes
* Nombres de usuario
* Administradores
* Moderadores
* Direcciones de correo electrónico
* Información del usuario
* Permisos
* Contraseñas
* Enlaces externos/internos
* Enlaces a archivos
* Contenidos específicos
* Valores

Datos sensibles como contraseñas pueden almacenarse en su forma en texto plano por MySQL; sin embargo, generalmente se encriptan previamente por los scripts PHP utilizando métodos seguros como la encriptación unidireccional.

### Comandos MySQL

Una base de datos MySQL traduce los comandos internamente en código ejecutable y realiza las acciones solicitadas. La aplicación web informa al usuario si ocurre un error durante el procesamiento, lo que diversos ataques de inyección SQL pueden provocar. A menudo, estas descripciones de errores contienen información importante y confirman, entre otras cosas, que la aplicación web interactúa con la base de datos de una manera diferente a la que los desarrolladores habían previsto.

La aplicación web envía la información generada de vuelta al cliente si los datos se procesan correctamente. Esta información puede ser los extractos de datos de una tabla o registros necesarios para procesamiento adicional con inicios de sesión, funciones de búsqueda, etc. Los comandos SQL pueden mostrar, modificar, agregar o eliminar filas en las tablas. Además, SQL también puede cambiar la estructura de las tablas, crear o eliminar relaciones e índices, y gestionar usuarios.

MariaDB, que a menudo se asocia con MySQL, es un fork del código original de MySQL. Esto se debe a que el principal desarrollador de MySQL dejó la empresa MySQL AB después de que fue adquirida por Oracle y desarrolló otro sistema de gestión de bases de datos SQL de código abierto basado en el código fuente de MySQL y lo llamó MariaDB.

## Configuración Predeterminada

La gestión de bases de datos SQL y sus configuraciones es un tema amplio. Es tan extenso que existen profesiones enteras, como el administrador de bases de datos, que se ocupan casi exclusivamente de bases de datos. Estas estructuras pueden crecer rápidamente y su planificación puede volverse complicada. Entre otras cosas, la gestión de bases de datos es una competencia central tanto para desarrolladores de software como para analistas de seguridad de la información. Cubrir esta área completamente iría más allá del alcance de este módulo. Por lo tanto, recomendamos configurar una instancia de MySQL/MariaDB para experimentar con las diversas configuraciones y entender mejor la funcionalidad y las opciones de configuración disponibles. Veamos la configuración predeterminada de MySQL.

```bash
sherlock28@htb[/htb]$ sudo apt install mysql-server -y
sherlock28@htb[/htb]$ cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'
```

```bash
[client]
port        = 3306
socket      = /var/run/mysqld/mysqld.sock

[mysqld_safe]
pid-file    = /var/run/mysqld/mysqld.pid
socket      = /var/run/mysqld/mysqld.sock
nice        = 0

[mysqld]
skip-host-cache
skip-name-resolve
user        = mysql
pid-file    = /var/run/mysqld/mysqld.pid
socket      = /var/run/mysqld/mysqld.sock
port        = 3306
basedir     = /usr
datadir     = /var/lib/mysql
tmpdir      = /tmp
lc-messages-dir = /usr/share/mysql
explicit_defaults_for_timestamp

symbolic-links=0

!includedir /etc/mysql/conf.d/
```

### Configuraciones Peligrosas

Muchos aspectos pueden estar mal configurados en MySQL. Podemos consultar más a fondo la referencia de MySQL para determinar qué opciones se pueden configurar en el archivo de configuración del servidor. Las principales opciones relevantes para la seguridad son:

| Configuración      | Descripción                                                                                                                     |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `user`             | Establece el usuario con el que se ejecutará el servicio MySQL.                                                                 |
| `password`         | Establece la contraseña para el usuario MySQL.                                                                                  |
| `admin_address`    | La dirección IP en la que escuchar conexiones TCP/IP en la interfaz de red administrativa.                                      |
| `debug`            | Esta variable indica la configuración actual de depuración.                                                                     |
| `sql_warnings`     | Esta variable controla si las declaraciones INSERT de una sola fila producen una cadena de información si ocurren advertencias. |
| `secure_file_priv` | Esta variable se utiliza para limitar el efecto de las operaciones de importación y exportación de datos.                       |

Las configuraciones `user`, `password` y `admin_address` son relevantes para la seguridad porque los valores se introducen en texto plano. A menudo, los permisos para el archivo de configuración del servidor MySQL no se asignan correctamente. Si logramos acceder a estos archivos o incluso a una shell, podemos ver el archivo y el nombre de usuario y la contraseña del servidor MySQL. Si no hay otras medidas de seguridad para prevenir el acceso no autorizado, se puede ver y editar toda la base de datos y toda la información de los clientes existentes, direcciones de correo electrónico, contraseñas y datos personales.

Las configuraciones `debug` y `sql_warnings` proporcionan una salida de información detallada en caso de errores, que es esencial para el administrador pero no debería ser vista por otros. Esta información a menudo contiene contenido sensible, que podría ser detectado por prueba y error para identificar más posibilidades de ataque. Estos mensajes de error a menudo se muestran directamente en las aplicaciones web. En consecuencia, las inyecciones SQL podrían ser manipuladas incluso para hacer que el servidor MySQL ejecute comandos del sistema. Esto se discute y se muestra en el módulo Fundamentos de Inyección SQL y Esenciales de SQLMap.

## Huella del Servicio

Existen muchas razones por las que un servidor MySQL podría ser accesible desde una red externa. Sin embargo, esto está lejos de ser una de las mejores prácticas y siempre podemos encontrar bases de datos a las que podemos acceder. A menudo, estas configuraciones estaban destinadas a ser temporales pero fueron olvidadas por los administradores. Esta configuración del servidor también podría usarse como una solución alternativa debido a un problema técnico. Normalmente, el servidor MySQL se ejecuta en el puerto TCP 3306, y podemos escanear este puerto con Nmap para obtener información más detallada.

#### Escaneo del Servidor MySQL

MySQL

```
bash
```
