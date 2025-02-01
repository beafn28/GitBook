# SQL Injection

### ¿Qué es la inyección SQL (SQLi)?

La inyección SQL (SQLi) es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación realiza a su base de datos. Esto puede permitirle:

* Ver datos que normalmente no debería acceder (credenciales, información personal, etc.).
* Modificar o eliminar datos.
* Escalar el ataque para comprometer el servidor o la infraestructura de backend.
* Realizar ataques de denegación de servicio (DoS).

### Impacto de un ataque exitoso

Un ataque SQLi exitoso puede provocar acceso no autorizado a:

* Contraseñas.
* Detalles de tarjetas de crédito.
* Información personal de los usuarios.

Históricamente, ha sido una de las principales causas de filtraciones de datos de alto perfil.

### Cómo detectar vulnerabilidades SQLi

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

### Ejemplos de ataques SQLi

#### Recuperación de datos ocultos

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

#### Bypass de autenticación

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

#### UNION attack para obtener datos de otras tablas

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

### Cómo prevenir SQL Injection

#### Consultas parametrizadas (Prepared Statements)

Las consultas deben usar parámetros en lugar de concatenación de cadenas:

```
PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();
```

#### Otras medidas de seguridad

* **Validación de entrada**: Asegurar que los valores recibidos sean del tipo y formato esperados.
* **Lista blanca de valores permitidos**: Especialmente en nombres de tablas, columnas y cláusulas como `ORDER BY`.
* **Principio de privilegios mínimos**: Limitar el acceso de las cuentas de la base de datos.
* **Monitorización y logging**: Detectar patrones anómalos en consultas SQL.
