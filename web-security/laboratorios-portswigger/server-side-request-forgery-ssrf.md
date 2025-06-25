# Server-side request forgery (SSRF)

## Lab: Basic SSRF against the local server

### Enunciado

Este laboratorio tiene una funcionalidad de **verificación de stock** que obtiene datos desde un **sistema interno**. **Modificar la URL usada en la verificación de stock para acceder a la interfaz de administración en `http://localhost/admin`** y **eliminar al usuario `carlos`**.

### Resolución

Entramos a un producto y comprobamos la petición.

<figure><img src="../../.gitbook/assets/image (1400).png" alt=""><figcaption></figcaption></figure>

Como el servidor web tiene esos permisos accedemos a la URL cambiandola ya que en el **/admin** nos decía que se podría entrar en localhost.

<figure><img src="../../.gitbook/assets/image (1401).png" alt=""><figcaption></figcaption></figure>

Ahora vemos la ruta para eliminar al usuario y hacemos lo mismo cambiando la URL para enviarlo y así se elimina.

<figure><img src="../../.gitbook/assets/image (1402).png" alt=""><figcaption></figcaption></figure>

## Lab: Basic SSRF against another back-end system

### Enunciado

Este laboratorio tiene una funcionalidad de **verificación de stock** que obtiene datos desde un **sistema interno**. Usar la funcionalidad de verificación de stock para **escanear el rango interno `192.168.0.X` en el puerto 8080**, localizar una **interfaz de administración**, y utilizarla para **eliminar al usuario `carlos`**.

### Resolución

No se encuentra **/admin** por lo que vamos a ver el stock de un producto y su petición.

<figure><img src="../../.gitbook/assets/image (1403).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1404).png" alt=""><figcaption></figcaption></figure>

Hacer peticiones a una IP eso está mal por lo que enviamos esa petición pero para la ruta **/admin**.

<figure><img src="../../.gitbook/assets/image (1405).png" alt=""><figcaption></figcaption></figure>

Nos da error por lo que no se encuentra en esa IP. Enviamos al **Intruder** y realizamos una fuerza bruta para ver que IP es.

<figure><img src="../../.gitbook/assets/image (1406).png" alt=""><figcaption></figcaption></figure>

Encontramos una solución que nos da código 200.

<figure><img src="../../.gitbook/assets/image (1407).png" alt=""><figcaption></figcaption></figure>

Con la petición del stock con la ruta de eliminar como hicimos antes pero con la IP correcta.

<figure><img src="../../.gitbook/assets/image (1408).png" alt=""><figcaption></figcaption></figure>
