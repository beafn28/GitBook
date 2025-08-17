# Information disclosure

## Lab: Information disclosure in error messages

### Enunciado

Los mensajes de error detallados en este laboratorio revelan que está utilizando una **versión vulnerable de un framework de terceros**. **Obtener y enviar el número de versión de dicho framework.**

### Resolución

La petición busca un número por lo que si forzamos el error en vez de un número ponemos un letra salta el error del servidor.

```
https://0a57002a03a3a181803e536600db00d4.web-security-academy.net/product?productId=a
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se nos muestra la versión del Apache que es la solución y sabemos que usa Java.

## Lab: Information disclosure on debug page

### Enunciado

Este laboratorio contiene una **página de depuración (debug)** que revela información sensible sobre la aplicación. **Obtener y enviar la variable de entorno `SECRET_KEY`.**

### Resolución

Siempre cuando nos presenten una web es recomendable ver el código fuente ya que puede mostrarnos información relevante en este caso un enlace comentado.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

También se nos muestra en Target, ahora buscamos ese enlace.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se nos muestra una página default de la versión del PHP pero en este laboratorio no se nos pide eso sino encontrar el valor de la `SECRET_KEY` por lo que buscamos dentro de la página.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Source code disclosure via backup files

### Enunciado

Este laboratorio **filtra su código fuente** a través de archivos de respaldo ubicados en un **directorio oculto**. **Identificar y enviar la contraseña de la base de datos**, la cual está **codificada directamente en el código fuente filtrado**.

### Resolución

Otra cosa que hay que mirar en estos casos para encontrar información es el fichero **robots.txt.**

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un directorio `/backup`. Por lo que accedemos a él.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abrimos el archivo.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Podemos ver el código fuente y vemos la información de una conexión de la base de datos como la contraseña.

## Lab: Authentication bypass via information disclosure

### Enunciado

La interfaz de administración de este laboratorio tiene una **vulnerabilidad de omisión de autenticación**, pero es **poco práctica de explotar sin conocer un encabezado HTTP personalizado** que utiliza el front-end.

1. **Descubrir el nombre del encabezado personalizado** que se utiliza para la autenticación.
2. Usarlo para **omitir la autenticación** y acceder a la interfaz de administración.
3. **Eliminar al usuario `carlos`.**

Credenciales disponibles:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos. Como vimos anteriormente en otros laboratorios hay una pestaña admin que nos dice lo siguiente.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Enviamos esa petición al **Repeater**. Cambiamos la petición de GET a TRACE para que el servidor nos devuelva en el cuerpo de la respuesta todos los encabezados que recibió, lo cual puede revelar el nombre del encabezado personalizado necesario para bypassar la autenticación.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos sale información de los encabezados. Vemos que también comprueba la IP es decir vemos que si es una Ip que no sea localhost es denegada por lo que modificamos ese encabezado con **Proxy**.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al interceptar añadimos el encabezado pero con la IP de localhost (no la pública).&#x20;

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Para eliminarle también tenemos que poner ese encabezado y enviarlo.

<figure><img src="../../.gitbook/assets/image (1391).png" alt=""><figcaption></figcaption></figure>

## Lab: Information disclosure in version control history

### Enunciado

Este laboratorio **divulga información sensible a través del historial de control de versiones**. **Obtener la contraseña del usuario administrador** desde ese historial. Iniciar sesión como administrador. **Eliminar al usuario `carlos`.**

### Resolución

Escaneamos los directorios con **Dirbuster**.

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Encontramos un directorio interesante que es el **.git.**

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisando encontramos en logs información sobre los commits.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Para ver el contenido nos lo descargamos.

```bash
wget -r [LAB-URL]/.git
git log
git show 46d727cdb91d161e7eed099b7d532fd252975a36
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales y borramos al usuario.

<figure><img src="../../.gitbook/assets/image (1390).png" alt=""><figcaption></figcaption></figure>
