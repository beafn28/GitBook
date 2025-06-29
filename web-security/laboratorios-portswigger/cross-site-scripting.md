# Cross-site scripting

## Lab: Reflected XSS into HTML context with nothing encoded

### Enunciado

Este laboratorio contiene una vulnerabilidad sencilla de Cross-Site Scripting (XSS) reflejado en la funcionalidad de búsqueda. Para resolver el laboratorio, realiza un ataque de XSS que invoque la función **`alert`.**

### Resolución

Es un XSS reflejado por lo que viene de la solicitud de HTTP el ataque. Si ponemos en el buscador algo luego se refleja en la web por lo que realizamos este payload.

```
<SCRIPT>alert("XSS")</SCRIPT>"\>
```

<figure><img src="../../.gitbook/assets/image (1471).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1470).png" alt=""><figcaption></figcaption></figure>

## Lab: Stored XSS into HTML context with nothing encoded

### Enunciado

Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) almacenado en la funcionalidad de comentarios.

Para resolver el laboratorio, envía un comentario que invoque la función `alert` cuando se visualice la entrada del blog.

### Resolución

Vamos a escribir un comentario con el payload del anterior laboratorio.

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `document.write` sink using source `location.search`

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de seguimiento de consultas de búsqueda.**

Utiliza la función **document.write** de JavaScript, que escribe datos en la página. La función **document.write** se llama con datos provenientes de **location.search**, los cuales puedes controlar mediante la URL del sitio web.

**Para resolver el laboratorio, realiza un ataque de XSS que invoque la función `alert`.**

### Resolución

Observamos que al buscar se refleja en la página pero no de la misma manera por lo que inspeccionamos a ver qué pasa.

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

Lo que hemos buscado se encuentra en el atributo **src** de una imagen por lo que realizamos el siguiente payload.

```
"><img src=x onerror=alert('MardukWasHere')>
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `innerHTML` sink using source `location.search`

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de búsqueda del blog.**

Utiliza una asignación a **innerHTML**, que modifica el contenido HTML de un elemento **div** usando datos provenientes de **location.search**.

**Para resolver el laboratorio, realiza un ataque de XSS que invoque la función `alert`.**

### Resolución

Lo primero es buscar algo y ver dónde y cómo se almacena.

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Se ve reflejado lo que buscamos en la web y se almacena en el **innerHTML** de la etiqueta **\<span>**. Sabiendo esto realizamos el siguiente payload.

```bash
</span><img src=/ onerror=alert(1) />//
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in jQuery anchor `href` attribute sink using `location.search` source

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la página de envío de comentarios (**_**submit feedback**_**).**

Utiliza la función **$** de la biblioteca jQuery para encontrar un elemento de enlace (_anchor_) y cambia su atributo **href** usando datos provenientes de **location.search**.

**Para resolver el laboratorio, haz que el enlace de "back" (**_**volver**_**) ejecute `alert(document.cookie)`.**

### Resolución

Escribimos un feedback para ver dónde está la vulnerabilidad.

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que por defecto nos añade el parámetro **returnPath** y añadimos cualquier valor.

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

No sucede nada pero si ponemos el ratón sobre **Back** se ha implementado en el atributo **href**. Sabiendo esto realizamos el siguiente payload.

```
javascript:alert(document.cookie)
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in jQuery selector sink using a hashchange event

### Enunciado

This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property.

To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser.

### Resolución

Esta vez no tenemos ninguna barra de búsqueda ni nada por lo que revisamos el código fuente.

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

Este código, en esencia, toma el valor que se encuentra después del símbolo **#** en la URL y lo utiliza para buscar ese término dentro de la página. Una vez que lo localiza, realiza un desplazamiento automático (scroll) hasta la sección donde se encuentra.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Sabiendo esto realizamos este payload.

```
<iframe src="https://0aac008603b76d4880f22bb200c80064.web-security-academy.net/#" onload="this.src+='<img src=/ onerror=print()>'"></iframe>
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Al pulsar el botón de **Deliver exploit to victim** completamos el laboratorio.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-29 103133.png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into attribute with angle brackets HTML-encoded

### Enunciado

Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) reflejado en la funcionalidad de búsqueda del blog, donde los signos de mayor y menor (< y >) se codifican en HTML.

Para resolver el laboratorio, realiza un ataque de XSS que inyecte un atributo y llame a la función `alert`.

### Resolución

Comprobamos que se refleja en el navegador. Vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

No podemos hacer los payloads normales porque los signos > y < nos lo urlcodeapor lo que realizamos que tras unas simples comillas en el código fuente se deja un espacio y en ese espacio aprovechamos para añadir un payload.

```
test" mirar código fuente espacio
test"onmouseover='alert(1)'
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Lab: Stored XSS into anchor `href` attribute with double quotes HTML-encoded

### Enunciado

Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) almacenado en la funcionalidad de comentarios.

Para resolver el laboratorio, envía un comentario que invoque la función `alert` cuando se haga clic en el nombre del autor del comentario.

### Resolución

Vamos a escribir un comentario.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Si pulsamos se nos redirige a la website que hemos puesto al escribir el comentario entonces ahí pondremos el payload.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Ponemos el siguiente payload en el campo website.

```
javascript:alert(1)
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) reflejado en la funcionalidad de seguimiento de consultas de búsqueda, donde los signos de mayor y menor (< y >) están codificados.** La reflexión del parámetro ocurre dentro de una **cadena de texto en JavaScript**. **Para resolver el laboratorio, realiza un ataque de XSS que salga de la cadena de JavaScript e invoque la función `alert`.**

### Resolución

Buscamos algo y vemos el código fuente.  &#x20;

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Si ponemos una comilla simple después de algo nos da error pero no se ejecuta. Realizamos este payload.

```
'+alert(1)+'
';alert(1);'
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de verificación de stock.**

Utiliza la función **document.write** de JavaScript, que escribe datos en la página. La función **document.write** se llama con datos tomados de **location.search**, los cuales puedes controlar mediante la URL del sitio web.

Los datos se insertan dentro de un elemento **select**.

**Para resolver el laboratorio, realiza un ataque de XSS que salga del elemento select e invoque la función `alert`.**

### Resolución

Vemos el códsigo fuente del stock del producto.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Viendo el código si añadimos en el enlace lo siguiente vemos que se almacena y se crea.

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Podemos modificar ese desplegable por lo que realizamos este payload.

```
</select><script>alert(1)</script>
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en una expresión de AngularJS dentro de la funcionalidad de búsqueda.**

**AngularJS** es una biblioteca popular de JavaScript que analiza el contenido de los nodos HTML que contienen el atributo **ng-app** (también conocido como directiva de AngularJS).

Cuando se añade una directiva al código HTML, puedes ejecutar expresiones de JavaScript dentro de **dobles llaves** (`{{ }}`). Esta técnica resulta útil cuando los signos de mayor y menor (< y >) se codifican.

**Para resolver el laboratorio, realiza un ataque de XSS que ejecute una expresión de AngularJS e invoque la función `alert`.**

### Resolución

Buscamos algo y vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

En el **body** se define el atributo **ng-app**, que indica el elemento raíz de AngularJS y marca el inicio de la aplicación. Al establecerlo en el **body**, todo el contenido dentro de esa etiqueta será procesado como parte de la aplicación Angular, interpretando cualquier expresión de Angular que se incluya.

Gracias a la funcionalidad de búsqueda, podemos controlar un parámetro que se inserta dentro de esta sección gestionada por Angular.

Para explotar esto, utilizamos el payload:

```
{{constructor.constructor('alert(1)')()}}
```

* Los dobles corchetes `{{ }}` hacen que Angular evalúe la expresión.
* `constructor.constructor` permite crear y ejecutar código arbitrario, como si definiéramos una nueva función cuyo contenido será `alert(1)`.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected DOM XSS

### Enunciado

**Este laboratorio demuestra una vulnerabilidad de tipo DOM XSS reflejado.**

Las vulnerabilidades DOM reflejadas ocurren cuando la aplicación del lado del servidor procesa datos de una solicitud y los devuelve en la respuesta. Luego, un script en la página maneja esos datos reflejados de forma insegura y los escribe en un _sink_ peligroso.

**Para resolver el laboratorio, crea una inyección que invoque la función `alert()`.**

### Resolución

Vemos las peticiones para analizarlas y vemos que la de búsqueda no tiene nada interesante pero la petición que se genera en el frontend sí.

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Se nos devuelve la respuesta en formato JSON por lo que intentamos meter un alert.

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

No hay problema ya que se inyecta sin problema. Buscamos con el siguiente payload.

```
test\"-alert(1)}//
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

## Lab: Stored DOM XSS

### Enunciado

Este laboratorio demuestra una vulnerabilidad de tipo DOM XSS almacenado en la funcionalidad de comentarios del blog. Para resolver el laboratorio, explota esta vulnerabilidad para invocar la función `alert()`.

### Resolución

Escribimos un comentario para comprender el funcionamiento.

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Se ha publicado sin problemas. Vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Aquí está el problema: se usa el método **replace**, que solo sustituye la **primera coincidencia**. Por ejemplo, si aplicamos `replace` para cambiar la **a** por **e** en "rascar", obtendremos "rescar".

Sabiendo esto, podemos diseñar un payload XSS típico, pero poniendo los signos **<>** al inicio, para que sean esos los que el script reemplace y no afecte al resto del código malicioso.

```
<><img src=x onerror=alert(1)> #en comentario
```

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>
