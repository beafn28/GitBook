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

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

