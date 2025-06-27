# SQL Injection

## Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

### Enunciado

Este laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de productos. Cuando el usuario selecciona una categoría, la aplicación realiza una consulta SQL como la siguiente:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Para resolver el laboratorio, realiza un ataque de inyección SQL que provoque que la aplicación muestre uno o más productos **no publicados**.

### Resolución

Al capturar la petición vemos que hay un error que no se contempla ya que si ponemos una comilla simple detrás de **Gifts** comentamos el resto de la sentencia.

```
SELECT * FROM products WHERE category = 'Gifts'' AND released = 1
```

Sabiendo esto hacemos la búsqueda:

```bash
?category=Gifts'--
```

Podemos observar como salen 4 elementos cuando salían 3 anteriormente.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Podemos ver que hay una inyección por lo que realizamos esta sentencia.

```
SELECT * FROM products WHERE category = 'Gifts' AND 1=1--' AND released = 1
```

Lo añadimos en la búsqueda y vemos que es vulnerable el parámetro **categories**. Volviendo al enunciado realizamos esta sentencia para resolverlo.

```
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```

Lo ponemos en la búsqueda.

```
?category=Gifts' OR 1=1--'
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection vulnerability allowing login bypass

### Enunciado

Este laboratorio contiene una vulnerabilidad de inyección SQL en la función de inicio de sesión.

Para resolver el laboratorio, realiza un ataque de inyección SQL que inicie sesión en la aplicación como el usuario **administrador**.

### Resolución

Lo primero que probamos que el usuario no es la misma contraseña. La consulta sería:&#x20;

```
SELECT * FROM usuarios WHERE username='administrator' AND contraseña= 'administrator'
```

Pero no sabemos la contraseña. Como dijimos anteriormente si metemos una comilla simple en el campo de **password** comprobamos que es vulnerable.

```
SELECT * FROM usuarios WHERE username='administrator' AND contraseña= '' OR 1=1--'
```

Por lo tanto insertamos eso en el login.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection attack, querying the database type and version on Oracle

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Puedes usar un ataque **UNION** para recuperar los resultados de una consulta inyectada.

### Resolución

Mandamos a **Repeater** la petición cuando filtras por categoría. Primero vemos si es vulnerable.

<figure><img src="../../.gitbook/assets/image (1409).png" alt=""><figcaption></figcaption></figure>

Vale si es vulnerable entonces vamos a ver cuántas columnas hay. Al probar vemos que hay 2 columnas.

<figure><img src="../../.gitbook/assets/image (1410).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto tenemos que saber en qué columna está la versión. También como está en otra consulta distinta usamos UNION.

<figure><img src="../../.gitbook/assets/image (1411).png" alt=""><figcaption></figcaption></figure>

Vemos que hemos insertado de manera correcta. Viendo el cheatsheet de PortSwigger nos dice donde se encuentra.

<figure><img src="../../.gitbook/assets/image (1412).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto hacemos la consulta siguiente.

<figure><img src="../../.gitbook/assets/image (1413).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Puedes usar un **ataque UNION** para recuperar los resultados de una consulta inyectada. **Mostrar la cadena de versión de la base de datos.**

### Resolución

Hacemos lo mismo que al principio del anterior, es decir, tenemos que ver cuántas columnas hay disponibles.&#x20;

<figure><img src="../../.gitbook/assets/image (1414).png" alt=""><figcaption></figcaption></figure>

Ya sabemos que hay dos columnas, ahora vamos a ver si se pueden almacenar texto.&#x20;

<figure><img src="../../.gitbook/assets/image (1415).png" alt=""><figcaption></figcaption></figure>

Sí se puede ya que devuelven string así que vamos a sacar la versión a través de SELECT.

<figure><img src="../../.gitbook/assets/image (1416).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection attack, listing the database contents on non-Oracle databases

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puedes usar un **ataque UNION** para recuperar datos de otras tablas.

La aplicación tiene una función de inicio de sesión, y la base de datos contiene una tabla que almacena **nombres de usuario y contraseñas**. Debes:

1. Determinar el **nombre de esa tabla** y los **campos (columnas)** que contiene.
2. Recuperar el contenido de la tabla para obtener los **nombres de usuario y contraseñas**.
3. Iniciar sesión como el usuario **administrador**.

**Iniciar sesión como el usuario administrador** para resolver el laboratorio.

### Resolución

Como en los anteriores laboratorios vemos cuántas columnas hay.

<figure><img src="../../.gitbook/assets/image (1417).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos 2 columnas por lo que ahora vamos a mirar si aceptan cadenas de texto.

<figure><img src="../../.gitbook/assets/image (1418).png" alt=""><figcaption></figcaption></figure>

Ahora tenemos que extraer las tablas ya que tenemos que conseguir las credenciales. Tenemos que darnos cuenta que es una base de datos No-Oracle.

<figure><img src="../../.gitbook/assets/image (1419).png" alt=""><figcaption></figcaption></figure>

Podemos ver que se nos muestra las siguientes tablas. Nos interesan las de usuarios.

<figure><img src="../../.gitbook/assets/image (1420).png" alt=""><figcaption></figcaption></figure>

Hacemos la consulta sabiendo el nombre de la tabla queremos saber las columnas.

<figure><img src="../../.gitbook/assets/image (1421).png" alt=""><figcaption></figcaption></figure>

Tenemos el nombre de las columnas tanto los usuarios, contraseñas y contraseñas por lo que realizamos la consulta.

<pre><code><strong>'+UNION+SELECT+username_oybnyq,+password_mtteee+FROM+users_zvzlug--
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1422).png" alt=""><figcaption></figcaption></figure>

Tenemos las credenciales y nos logueamos.

<figure><img src="../../.gitbook/assets/image (1423).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection attack, listing the database contents on Oracle

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puedes usar un **ataque UNION** para recuperar datos de otras tablas.

La aplicación tiene una función de inicio de sesión, y la base de datos contiene una tabla que almacena **nombres de usuario y contraseñas**. Debes:

1. **Determinar el nombre de la tabla** que contiene estas credenciales.
2. **Identificar las columnas** relevantes (como `username` y `password`).
3. **Extraer los datos de esa tabla** usando UNION SELECT.
4. **Iniciar sesión como el usuario administrador** para resolver el laboratorio.

### Resolución

Miramos como siempre cuantas columnas hay como podemos ver es una base de datos Oracle por lo que hay que tenerlo en cuenta.

<figure><img src="../../.gitbook/assets/image (1424).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto sabemos que hay al menos 2 columnas. Vamos a ver si se almacenan cadenas de texto.

<figure><img src="../../.gitbook/assets/image (1425).png" alt=""><figcaption></figcaption></figure>

Sí se almacenan cadenas de texto y sabiendo esto extraemos el nombre de las tablas para la base de datos de Oracle.

<figure><img src="../../.gitbook/assets/image (1426).png" alt=""><figcaption></figcaption></figure>

Como podemos ver hemos conseguido extraerlas pero claramente nos interesa la de usuarios.

<figure><img src="../../.gitbook/assets/image (1427).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto realizamos la consulta a esta tabla para saber las columnas.

<figure><img src="../../.gitbook/assets/image (1428).png" alt=""><figcaption></figcaption></figure>

Tenemos el nombre de las columnas para realizar la consulta y saber las credenciales.

<figure><img src="../../.gitbook/assets/image (1429).png" alt=""><figcaption></figcaption></figure>

Conseguimos las credenciales ya nos podemos loguear.

<figure><img src="../../.gitbook/assets/image (1430).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection UNION attack, finding a column containing text

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puedes usar un **ataque UNION** para recuperar datos de otras tablas.

Para construir este tipo de ataque, primero debes **determinar el número de columnas** que devuelve la consulta. Puedes hacerlo usando una técnica aprendida en un laboratorio anterior (por ejemplo, usando `ORDER BY` o probando combinaciones de columnas en el `UNION`).

El siguiente paso es **identificar qué columnas son compatibles con datos de tipo cadena (string)**.

El laboratorio proporcionará un **valor aleatorio** que debes hacer aparecer en los resultados de la consulta. Realizar un **ataque UNION SQL** que **devuelva una fila adicional con el valor proporcionado**, lo que te ayudará a identificar **qué columnas aceptan datos tipo string**.

### Resolución

Primero de todo vemos que contiene 3 columnas.

<figure><img src="../../.gitbook/assets/image (1431).png" alt=""><figcaption></figcaption></figure>

Seguimos viendo que columnas acpetan cadenas de texto.

<figure><img src="../../.gitbook/assets/image (1432).png" alt=""><figcaption></figcaption></figure>

Vemos que en la página principal nos da una cadena de texto por lo que la ponemos en la consulta.

<figure><img src="../../.gitbook/assets/image (1433).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection UNION attack, retrieving data from other tables

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puedes usar un **ataque UNION** para recuperar datos de otras tablas.

Para construir este ataque, deberás **combinar varias técnicas** que aprendiste en laboratorios anteriores.

La base de datos contiene una tabla diferente llamada **`users`**, con columnas **`username`** y **`password`**.

Realizar un **ataque de inyección SQL con UNION** para recuperar **todos los nombres de usuario y contraseñas**, y usar esa información para **iniciar sesión como el usuario administrador**.

### Resolución

Vamos a ver cuántas columnas hay.

<figure><img src="../../.gitbook/assets/image (1434).png" alt=""><figcaption></figcaption></figure>

Vemos que tenemos 2 columnas por lo que ahora toca ver si aceptan cadenas texto.

<figure><img src="../../.gitbook/assets/image (1435).png" alt=""><figcaption></figcaption></figure>

Como podemos si acepta así que suponemos que tiene dos columnas de usuarios y contraseñas.

<figure><img src="../../.gitbook/assets/image (1436).png" alt=""><figcaption></figcaption></figure>

Hemos conseguido las credenciales así que ya nos podemos loguear.

<figure><img src="../../.gitbook/assets/image (1437).png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection UNION attack, retrieving multiple values in a single column

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL** en el filtro de categoría de productos. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puedes usar un **ataque UNION** para recuperar datos de otras tablas.

La base de datos contiene una tabla llamada **`users`**, con las columnas **`username`** y **`password`**. Realizar un **ataque de inyección SQL con UNION** que recupere **todos los nombres de usuario y contraseñas**, y usar esa información para **iniciar sesión como el usuario `administrator`**.

### Resolución

Vemos cuántas columnas tiene.

<figure><img src="../../.gitbook/assets/image (1438).png" alt=""><figcaption></figcaption></figure>

Como podemos observar son 2 columnas, tras esto vemos si aceptan cadenas texto.

<figure><img src="../../.gitbook/assets/image (1439).png" alt=""><figcaption></figcaption></figure>

La segunda columna es la que sí acepta cadena de texto pero claro la información estará junta por lo que vamos a separarla.

<figure><img src="../../.gitbook/assets/image (1440).png" alt=""><figcaption></figcaption></figure>

Ya hemos conseguido las credenciales, vamos a loguearnos.

<figure><img src="../../.gitbook/assets/image (1441).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind SQL injection with conditional responses

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL ciega (blind SQL injection)**. La aplicación usa una **cookie de seguimiento** para análisis, y realiza una consulta SQL que incluye el valor de esa cookie.

Los resultados de la consulta **no se devuelven directamente** y **no se muestran mensajes de error**. Sin embargo, la aplicación **muestra un mensaje de "Welcome back" en la página si la consulta devuelve alguna fila**.

La base de datos contiene una tabla diferente llamada **`users`**, con columnas **`username`** y **`password`**. **Explotar la vulnerabilidad de inyección SQL ciega para averiguar la contraseña del usuario `administrator`, e iniciar sesión como dicho usuario.**

### Resolución

A diferencia de los laboratorios anteriores la vulnerabilidad está en **TrackingID**.

```
ID'+and+(select+username+from+users+where+username%3d'administrator'+and+LENGTH(password)>1)%3d'administrator'--;
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```
ID' and (select substring (password,1,1) from users where username='administrator')='a'--
```

