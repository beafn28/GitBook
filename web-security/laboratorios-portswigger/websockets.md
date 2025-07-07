# WebSockets

## Lab: Manipulating WebSocket messages to exploit vulnerabilities

### Enunciado

Esta tienda en línea tiene una función de chat en vivo implementada con WebSockets. Los mensajes de chat que envíes son vistos en tiempo real por un agente de soporte.

Envía un mensaje a través de WebSocket que provoque un popup de `alert()` en el navegador del agente de soporte.

### Resolución

Como vemos los mensajes que enviamos no salen en el **HTTP history** sino en **WebSockets** ya que es lo que usa.

<figure><img src="../../.gitbook/assets/image (1524).png" alt=""><figcaption></figcaption></figure>

Interceptamos y vemos que nos cambia los símbolos pero antes de mandarlo los cambiamos nosotros.

<figure><img src="../../.gitbook/assets/image (1525).png" alt=""><figcaption></figcaption></figure>

Vemos que se ha enviado por lo que enviamos un payload.

```
<img src=error onerror='alert(document.domain)'>
```

<figure><img src="../../.gitbook/assets/image (1527).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1526).png" alt=""><figcaption></figcaption></figure>
