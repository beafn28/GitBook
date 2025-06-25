# XML external entity (XXE) injection

## Lab: Exploiting XXE using external entities to retrieve files

### Enunciado

Este laboratorio tiene una funcionalidad de **"Check stock"** que **analiza entradas XML** y devuelve cualquier valor inesperado en la respuesta. **Inyectar una entidad externa XML (XXE)** para **recuperar el contenido del archivo `/etc/passwd`**.

### Resolución

Accedemos a la petición tras comprobar el stock y la mandamos al **Repeater**.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Tiene contenido en **XML** que vamos a cambiar. Vamos a poner una etiqueta ya que no comprueba este tipo de inyección.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Cambiamos el contenido para que se nos imprima el **/etc/passwd**.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting XXE to perform SSRF attacks

### Enunciado

Este laboratorio tiene una funcionalidad de **"Check stock"** que **analiza entradas XML** y devuelve cualquier valor inesperado en la respuesta.

El servidor del laboratorio está ejecutando un **endpoint simulado de metadatos EC2** en la URL por defecto:\
&#xNAN;**`http://169.254.169.254/`**\
Este endpoint puede usarse para obtener información sobre la instancia, incluyendo **datos sensibles**. **Explotar la vulnerabilidad XXE para realizar un ataque SSRF** y obtener la **clave secreta de acceso (secret access key) del IAM** desde el endpoint de metadatos EC2.

### Resolución

Comprobamos lo del anterior laboratorio si se puede inyectar código XML.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Sí se puede por lo que ahora lo modificamos así.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Al enviarlo nos devuelve latest que puede ser un directorio en el que vamos a ir indagando hasta encontrar las credenciales.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Y así todo el rato hasta las credenciales.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
