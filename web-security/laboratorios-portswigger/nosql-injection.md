# NoSQL injection

## Lab: Detecting NoSQL injection

### Enunciado

El filtro de categorías de productos de este laboratorio está respaldado por una base de datos NoSQL MongoDB. Es vulnerable a inyección NoSQL. Realiza un ataque de inyección NoSQL que haga que la aplicación muestre productos no publicados.

### Resolución

Vemos si es vulnerable poniendo una comilla simple.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al ser vulnerable realizamos la inyección típica en SQL pero en NoSQL.

```
category=Gifts%27%20||1||%27
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting NoSQL operator injection to bypass authentication

### Enunciado

La funcionalidad de inicio de sesión de este laboratorio está respaldada por una base de datos NoSQL MongoDB. Es vulnerable a inyección NoSQL mediante el uso de operadores de MongoDB. Inicia sesión en la aplicación como el usuario administrador.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y vemos la petición.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Podemos intentar inyectar objetos en los campos de usuario y contraseña para probar inyecciones NoSQL.

```
{"username":{"$gt":""},"password":{"$gt":""}}
```

Bueno, eso es interesante: código de respuesta 500 y un error que indica que se devolvió un número inesperado de registros.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

La consulta que enviamos en realidad terminaría recuperando todos los registros de la base de datos. Y la base de datos tendría más de un registro, pero el código del backend esperaría que la consulta devolviera solo un registro. Vamos a evadir la autenticación aprovechando la inyección **NoSQL** que encontramos en la funcionalidad de inicio de sesión:

```
{"username":{"$regex":"^admin"},"password":{"$gt":""}}
```

Observa que obtuvimos un **código de respuesta 302** y no un 500.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Interceptamos con **Proxy** y con esa inyección damos a **Forward**.

<figure><img src="../../.gitbook/assets/image (1559).png" alt=""><figcaption></figcaption></figure>
