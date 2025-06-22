# Business logic vulnerabilities

## Lab: Excessive trust in client-side controls

### Enunciado

Este laboratorio **no valida adecuadamente la entrada del usuario**. Puedes explotar una **falla lógica** en el flujo de compra para adquirir artículos por un precio no previsto. Objetivo: **Comprar una "Lightweight l33t leather jacket".**

Credenciales disponibles:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y claro no tenemos dinero para comprar esa chaqueta. Vamos a comprarla como si podríamos de momento pero nos salta error.

Comprobamos mandando la petición al **Repeater** que cada vez que enviamos aumenta la cantidad de chaquetas y por lo tanto de precio. Volvamos a borrar el carrito para cambiar el precio.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

No nos da error sino que redirige como antes por lo que al actualizar la página vemos que ha cambiado precio.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Por lo que ahora ya podemos comprar la chaqueta.

<figure><img src="../../.gitbook/assets/image (1370).png" alt=""><figcaption></figcaption></figure>

