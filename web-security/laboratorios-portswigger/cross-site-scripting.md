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

