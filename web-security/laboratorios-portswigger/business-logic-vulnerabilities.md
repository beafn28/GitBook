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

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

No nos da error sino que redirige como antes por lo que al actualizar la página vemos que ha cambiado precio.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Por lo que ahora ya podemos comprar la chaqueta.

<figure><img src="../../.gitbook/assets/image (1370).png" alt=""><figcaption></figcaption></figure>

## Lab: High-level logic vulnerability

### Enunciado

Este laboratorio **no valida adecuadamente la entrada del usuario**. Puedes explotar una **falla lógica** en el flujo de compra para adquirir artículos a un **precio no previsto**. **Comprar una "Lightweight l33t leather jacket"** (una chaqueta de cuero l33t ligera).

Credenciales disponibles:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos con las credenciales y volvemos a intentar comprar esa chaqueta y nos sale error. Con respeto al anterior en la petición no sale el precio por lo que no lo podemos modificar.

<figure><img src="../../.gitbook/assets/image (1371).png" alt=""><figcaption></figcaption></figure>

Eliminamos la chaqueta añadida y miramos la petición siguiente en el que hay un número negativo. Lo mandamos a **Repeater**.

<figure><img src="../../.gitbook/assets/image (1372).png" alt=""><figcaption></figcaption></figure>

Lo enviamos pero nos dice que no debe ser menor que 0 ya que nos salía que el precio es negativo por lo que vamos a añadir un producto para aplicar la resta y así poder comprarla.

<figure><img src="../../.gitbook/assets/image (1373).png" alt=""><figcaption></figcaption></figure>

Enviamos la petición de manera que la cantidad sea negativa y recargamos la página.

<figure><img src="../../.gitbook/assets/image (1374).png" alt=""><figcaption></figcaption></figure>

Al volverla a enviar y recargar.

<figure><img src="../../.gitbook/assets/image (1375).png" alt=""><figcaption></figcaption></figure>

Vemos que se nos ha restado el precio por lo que aumentamos esa cantidad y va disminuyendo.

<figure><img src="../../.gitbook/assets/image (1376).png" alt=""><figcaption></figcaption></figure>

Ya la podemos comprar.

<figure><img src="../../.gitbook/assets/image (1377).png" alt=""><figcaption></figcaption></figure>

## Lab: Inconsistent security controls

### Enunciado

La lógica defectuosa de este laboratorio permite que **usuarios arbitrarios accedan a funcionalidades administrativas** que deberían estar disponibles solo para empleados de la empresa. **Acceder al panel de administración y eliminar al usuario `carlos`.**

### Resolución

Nos copiamos el correo.

<figure><img src="../../.gitbook/assets/image (1378).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Se podría usar si se tiene la versión de pago para descubrir las URLs en Target -> Engagement Tools -> Discovery Content pero no hace falta.
{% endhint %}

Usamos **dirbuster** para encontrar más enlaces.

```bash
dirb https://0a4000c70468f41e81f92b91005c008b.web-security-academy.net/
```

<figure><img src="../../.gitbook/assets/image (1389).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención este enlace.

<figure><img src="../../.gitbook/assets/image (1379).png" alt=""><figcaption></figcaption></figure>

No tenemos ningún user así por lo que nos registramos.

<figure><img src="../../.gitbook/assets/image (1380).png" alt=""><figcaption></figcaption></figure>

Activamos la cuenta e iniciamos sesión. Como podemos observar podemos actualizar el correo y en el panel de registro se nos dice como debe ser ese correo y claro así podemos entrar en la URL de admin.

<figure><img src="../../.gitbook/assets/image (1381).png" alt=""><figcaption></figcaption></figure>

Se actualiza perfectamente y se nos muestra el panel de admin que antes no se nos mostraba. Ya podemos eliminar al usuario carlos.

<figure><img src="../../.gitbook/assets/image (1382).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1383).png" alt=""><figcaption></figcaption></figure>

## Lab: Flawed enforcement of business rules

### Enunciado

Este laboratorio contiene una **falla lógica** en su flujo de compra. **Explotar esta falla para comprar una “Lightweight l33t leather jacket”.**

Credenciales disponibles:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Vemos que es la misma página pero nos sale un descuento. Nos lo copiamos y nos logueamos con las credenciales dadas. Compramos y metemos el cupón que se nos ha descontado 5 dolares.

<figure><img src="../../.gitbook/assets/image (1384).png" alt=""><figcaption></figcaption></figure>

La petición tiene un token, claramente tiene sentido para no usar ese descuento otra vez. Lo comprobamos y no se puede. Mientras revisamos la web nos podemos suscribir a una newsletter.&#x20;

<figure><img src="../../.gitbook/assets/image (1385).png" alt=""><figcaption></figcaption></figure>

Nos sale otro cupón al suscribirnos. Se nos aplica pero solo una vez.

<figure><img src="../../.gitbook/assets/image (1386).png" alt=""><figcaption></figcaption></figure>

Por lo que si observamos el error sale solo si metes otra vez el descuento que acabas de meter, es decir, si metes el otro se te vuelve a descontar. En resumen, vamos a ir metiendo intermitentemente uno y otro hasta poder comprar la chaqueta.

<figure><img src="../../.gitbook/assets/image (1387).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1388).png" alt=""><figcaption></figcaption></figure>
