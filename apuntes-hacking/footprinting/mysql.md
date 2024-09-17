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

### Escaneo del Servidor MySQL

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

**Resultado del escaneo:**

```less
Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 00:53 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE     VERSION
3306/tcp open  nagios-nsca Nagios NSCA
| mysql-brute: 
|   Accounts: 
|     root:<empty> - Valid credentials
|_  Statistics: Performed 45010 guesses in 5 seconds, average tps: 9002.0
|_mysql-databases: ERROR: Script execution failed (use -d to debug)
|_mysql-dump-hashes: ERROR: Script execution failed (use -d to debug)
| mysql-empty-password: 
|_  root account has empty password
| mysql-enum: 
|   Valid usernames: 
|     root:<empty> - Valid credentials
|     netadmin:<empty> - Valid credentials
|     guest:<empty> - Valid credentials
|     user:<empty> - Valid credentials
|     web:<empty> - Valid credentials
|     sysadmin:<empty> - Valid credentials
|     administrator:<empty> - Valid credentials
|     webadmin:<empty> - Valid credentials
|     admin:<empty> - Valid credentials
|     test:<empty> - Valid credentials
|_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.26-0ubuntu0.20.04.1
|   Thread ID: 13
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, SupportsTransactions, Speaks41ProtocolOld, LongPassword, DontAllowDatabaseTableColumn, Support41Auth, IgnoreSigpipes, SwitchToSSLAfterHandshake, FoundRows, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: YTSgMfqvx\x0F\x7F\x16\&\x1EAeK>0
|_  Auth Plugin Name: caching_sha2_password
|_mysql-users: ERROR: Script execution failed (use -d to debug)
|_mysql-variables: ERROR: Script execution failed (use -d to debug)
|_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.21 seconds
```

Como con todos nuestros escaneos, debemos tener cuidado con los resultados y confirmar manualmente la información obtenida porque algunos datos podrían resultar ser falsos positivos. Este escaneo es un excelente ejemplo de esto, ya que sabemos con certeza que el servidor MySQL objetivo no utiliza una contraseña vacía para el usuario root, sino una contraseña fija. Podemos comprobar esto con el siguiente comando:

### Interacción con el Servidor MySQL

#### Conexión Inicial

```bash
sherlock28@htb[/htb]$ mysql -u root -h 10.129.14.132
```

**Error:**

```sql
ERROR 1045 (28000): Access denied for user 'root'@'10.129.14.1' (using password: NO)
```

Por ejemplo, si usamos una contraseña que hemos adivinado o encontrado a través de nuestra investigación, podremos iniciar sesión en el servidor MySQL y ejecutar algunos comandos.

```bash
sherlock28@htb[/htb]$ mysql -u root -pP4SSw0rd -h 10.129.14.128
```

**Bienvenida:**

```python
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 150165
Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)                                                          
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.                                     
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.                            
```

#### Consultas Iniciales

```sql
MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.006 sec)

MySQL [(none)]> select version();
+-------------------------+
| version()               |
+-------------------------+
| 8.0.27-0ubuntu0.20.04.1 |
+-------------------------+
1 row in set (0.001 sec)

MySQL [(none)]> use mysql;
MySQL [mysql]> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
...SNIP...
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.002 sec)
```

Si revisamos las bases de datos existentes, veremos que ya hay varias. Las bases de datos más importantes para el servidor MySQL son los esquemas del sistema (`sys`) y del esquema de información (`information_schema`). El esquema del sistema contiene tablas, información y metadatos necesarios para la gestión. Más información sobre esta base de datos se puede encontrar en el manual de referencia de MySQL.

```sql
MySQL> use sys;
MySQL> show tables;

+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
...SNIP...
| x$waits_global_by_latency                     |
+-----------------------------------------------+

MySQL> select host, unique_users from host_summary;

+-------------+--------------+                   
| host        | unique_users |                   
+-------------+--------------+                   
| 10.129.14.1 |            1 |                   
| localhost   |            2 |                   
+-------------+--------------+                   
2 rows in set (0,01 sec)  
```

El esquema de información es también una base de datos que contiene metadatos. Sin embargo, estos metadatos se recuperan principalmente del esquema del sistema. La razón de la existencia de estos dos esquemas es el estándar ANSI/ISO que se ha establecido. El esquema del sistema es un catálogo del sistema de Microsoft para servidores SQL y contiene mucha más información que el esquema de información.

#### Comandos Útiles para MySQL

| Comando                                              | Descripción                                                                                |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `mysql -u <user> -p<password> -h <IP address>`       | Conectar al servidor MySQL. No debe haber un espacio entre la opción `-p` y la contraseña. |
| `show databases;`                                    | Mostrar todas las bases de datos.                                                          |
| `use <database>;`                                    | Seleccionar una de las bases de datos existentes.                                          |
| `show tables;`                                       | Mostrar todas las tablas disponibles en la base de datos seleccionada.                     |
| `show columns from <table>;`                         | Mostrar todas las columnas en la tabla seleccionada.                                       |
| `select * from <table>;`                             | Mostrar todo el contenido de la tabla deseada.                                             |
| `select * from <table> where <column> = "<string>";` | Buscar una cadena específica en la columna deseada de la tabla.                            |

Debemos saber cómo interactuar con diferentes bases de datos. Por lo tanto, recomendamos instalar y configurar un servidor MySQL en una de nuestras máquinas virtuales para experimentar. También hay una sección de problemas de seguridad ampliamente cubierta en el manual de referencia que trata sobre las mejores prácticas para asegurar los servidores MySQL. Deberíamos utilizar esto al configurar nuestro servidor MySQL para entender mejor por qué algo podría no funcionar.
