# Race conditions

## Lab: Limit overrun race conditions

### Enunciado

El flujo de compra de este laboratorio contiene una **condición de carrera (race condition)** que te permite comprar artículos a un **precio no previsto**. **Comprar con éxito una “Lightweight L33t Leather Jacket”.** Puedes iniciar sesión en tu cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

> Para una forma más rápida y conveniente de explotar la condición de carrera, se recomienda usar la acción personalizada **"Trigger race conditions"**, disponible únicamente en **Burp Suite Professional**.

### Resolución&#x20;

Nos logueamos y añadimos el cupón cuando añadimos el carrito pero no podemos aún comprarla. Mandamos la petición a **Repeater**. Para explotar necesitamos mandar varias peticiones a la vez. Hacemos `Ctrl+R` como 50 veces. Creamos un grupo seleccionando todas las peticiones y las enviamos.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Recargamos la página.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ya podemos comprarla se ha aplicado el descuento varias veces.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
