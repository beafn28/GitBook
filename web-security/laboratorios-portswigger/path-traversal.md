# Path Traversal

## Lab: File path traversal, simple case

### Enunciado

Este laboratorio contiene una vulnerabilidad de **recorrido de directorios (path traversal)** en la visualización de imágenes de productos. **Extraer el contenido del archivo `/etc/passwd`.**

### Resolución

En el enunciado nos comenta que esto es vulnerable:&#x20;

```
image?filename=33.jpg
```

Por lo que si el contenido no está correctamente filtrado se podrá a acceder a otras cosas como en este caso lo que nos pide el enunciado para ello realizamos lo siguiente en el **Repeater**.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que el error que muestre es que no encuentra el archivo cosa a nuestro favor para ello realizamos lo siguiente:

```
../../../etc/passwd
```

{% hint style="info" %}
Se podrá ../ tantas veces ya que a priori no sabemos donde se encuentra. Podemos también hacerlo con Intruder y ver cuando cambia la respuesta.
{% endhint %}

Sabiendo esto y al enviar varias peticiones encontramos la solución.

<figure><img src="../../.gitbook/assets/image (1369).png" alt=""><figcaption></figcaption></figure>

## Lab: File path traversal, traversal sequences blocked with absolute path bypass

### Enunciado

Este laboratorio contiene una **vulnerabilidad de recorrido de directorios (path traversal)** en la visualización de imágenes de productos.

La aplicación **bloquea las secuencias de recorrido (`../`)**, pero **trata el nombre de archivo proporcionado como relativo a un directorio de trabajo predeterminado**. **Recuperar el contenido del archivo `/etc/passwd`**.

### Resolución

Identificamos un endpoint vulnerable a path traversal en el parámetro `filename`, que sirve imágenes. Aunque bloquea rutas absolutas (`/etc/passwd`), logramos evadir el filtro:

<pre><code><strong>/etc/passwd
</strong></code></pre>

El servidor resolvió la ruta y devolvió el contenido del archivo `/etc/passwd`, confirmando la vulnerabilidad. Extraímos datos sensibles, como las rutas de los usuarios del sistema, completando el objetivo del laboratorio.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: File path traversal, traversal sequences stripped non-recursively

### Enunciado

Este laboratorio contiene una vulnerabilidad de path traversal en la visualización de imágenes de productos.

La aplicación elimina las secuencias de path traversal del nombre de archivo proporcionado por el usuario antes de usarlo.

Para resolver el laboratorio, recupera el contenido del archivo /etc/passwd.

### Resolución

Identificamos una vulnerabilidad de path traversal en la carga de imágenes del producto. Aunque la aplicación intentaba filtrar las secuencias `../`, descubrimos que su eliminación no era recursiva. Aprovechamos esto encadenando varias secuencias truncadas para evadir el filtro y acceder al archivo `/etc/passwd`, verificando así la lectura arbitraria de archivos fuera del directorio previsto y resolviendo el laboratorio.

```
....//....//....//....//etc/passwd
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: File path traversal, traversal sequences stripped with superfluous URL-decode

### Enunciado

Este laboratorio contiene una vulnerabilidad de path traversal en la visualización de imágenes de productos.

La aplicación bloquea la entrada que contiene secuencias de path traversal. Luego realiza una decodificación de URL en la entrada antes de usarla.

Para resolver el laboratorio, recupera el contenido del archivo /etc/passwd.

### Resolución

Identificamos que la aplicación filtra secuencias de path traversal clásicas (`../`) antes de decodificar la entrada. Para evadir el filtro, enviamos la secuencia doblemente codificada (`..%252f`) que se decodifica en una sola pasada a `../` en el servidor. Usamos la carga útil:

```
/image?filename=..%252f..%252f..%252f..%252f..%25fetc/passwd
```

Esto permitió acceder y leer el contenido de `/etc/passwd`, demostrando la vulnerabilidad de path traversal con decodificación adicional en el backend.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: File path traversal, validation of start of path

### Enunciado

Este laboratorio contiene una vulnerabilidad de path traversal en la visualización de imágenes de productos.

La aplicación transmite la ruta completa del archivo mediante un parámetro en la solicitud y valida que la ruta proporcionada comience con la carpeta esperada.

Para resolver el laboratorio, recupera el contenido del archivo **/etc/passwd**.

### Resolución

Aprovechamos que la aplicación solo valida que la ruta comience con el directorio esperado sin normalizarla, usando `/var/www/images/../../../etc/passwd` para evadir la comprobación y acceder al contenido de `/etc/passwd`, demostrando así la vulnerabilidad y resolviendo el lab.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: File path traversal, validation of file extension with null byte bypass

### Enunciado

Este laboratorio contiene una vulnerabilidad de path traversal en la visualización de imágenes de productos.

La aplicación valida que el nombre de archivo proporcionado termine con la extensión de archivo esperada.

Para resolver el laboratorio, recupera el contenido del archivo /etc/passwd.

### Resolución

Para explotar la validación de extensión con un bypass por byte nulo se envió en el parámetro filename una ruta con secuencias de path traversal hacia **/etc/passwd** seguida de **%00.png** para engañar la comprobación de extensión la validación solo revisa que termine en .png antes de interpretar la cadena por lo que %00 actúa como terminador nulo al decodificarse en el backend esto permitió recuperar el contenido del archivo sensible y resolver el laboratorio.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
