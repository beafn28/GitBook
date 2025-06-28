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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `document.write` sink using source `location.search`

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de seguimiento de consultas de búsqueda.**

Utiliza la función **document.write** de JavaScript, que escribe datos en la página. La función **document.write** se llama con datos provenientes de **location.search**, los cuales puedes controlar mediante la URL del sitio web.

**Para resolver el laboratorio, realiza un ataque de XSS que invoque la función `alert`.**

### Resolución

Observamos que al buscar se refleja en la página pero no de la misma manera por lo que inspeccionamos a ver qué pasa.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Lo que hemos buscado se encuentra en el atributo **src** de una imagen por lo que realizamos el siguiente payload.

```
"><img src=x onerror=alert('MardukWasHere')>
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `innerHTML` sink using source `location.search`

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de búsqueda del blog.**

Utiliza una asignación a **innerHTML**, que modifica el contenido HTML de un elemento **div** usando datos provenientes de **location.search**.

**Para resolver el laboratorio, realiza un ataque de XSS que invoque la función `alert`.**

### Resolución

Lo primero es buscar algo y ver dónde y cómo se almacena.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Se ve reflejado lo que buscamos en la web y se almacena en el **innerHTML** de la etiqueta **\<span>**. Sabiendo esto realizamos el siguiente payload.

```bash
</span><img src=/ onerror=alert(1) />//
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in jQuery anchor `href` attribute sink using `location.search` source

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la página de envío de comentarios (**_**submit feedback**_**).**

Utiliza la función **$** de la biblioteca jQuery para encontrar un elemento de enlace (_anchor_) y cambia su atributo **href** usando datos provenientes de **location.search**.

**Para resolver el laboratorio, haz que el enlace de "back" (**_**volver**_**) ejecute `alert(document.cookie)`.**

### Resolución

Escribimos un feedback para ver dónde está la vulnerabilidad.

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Vemos que por defecto nos añade el parámetro **returnPath** y añadimos cualquier valor.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

No sucede nada pero si ponemos el ratón sobre **Back** se ha implementado en el atributo **href**. Sabiendo esto realizamos el siguiente payload.

```
javascript:alert(document.cookie)
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in jQuery selector sink using a hashchange event

### Enunciado

This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property.

To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser.

### RESOLUCIÓN

Esta vez no tenemos ninguna barra de búsqueda ni nada por lo que revisamos el código fuente.

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Este código, en esencia, toma el valor que se encuentra después del símbolo **#** en la URL y lo utiliza para buscar ese término dentro de la página. Una vez que lo localiza, realiza un desplazamiento automático (scroll) hasta la sección donde se encuentra.

Por ejemplo, si nos dirigimos al final del laboratorio, veremos una publicación cuyo título incluye la palabra **“World”**, y el navegador se desplazará automáticamente hasta ella.

