# Insecure deserialization

## Lab: Modifying serialized objects

### Enunciado

Este laboratorio utiliza un mecanismo de sesión basado en serialización y es vulnerable a escalada de privilegios como resultado. Edita el objeto serializado en la cookie de sesión para explotar esta vulnerabilidad y obtener privilegios de administrador. Luego, elimina al usuario carlos.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos.

<figure><img src="../../.gitbook/assets/image (1528).png" alt=""><figcaption></figcaption></figure>

Vamos a decodificar la cookie ya que en el enunciado dice que ahí está la vulnerabilidad.

<figure><img src="../../.gitbook/assets/image (1529).png" alt=""><figcaption></figcaption></figure>

Esto nos indica que wiener no tiene permisos de administrador por ese 0 que indica falso por lo que lo cambiamos a 1.

<figure><img src="../../.gitbook/assets/image (1530).png" alt=""><figcaption></figcaption></figure>

Cambiamos la cookie y al enviar la petición no sale un panel de administración pero si desconectamos nos rechaza.

<figure><img src="../../.gitbook/assets/image (1531).png" alt=""><figcaption></figcaption></figure>

Por lo que la manera de que en cada petición poner la cookie es un rollo con lo  cual la otra solución es ir a inspeccionar y en aplicación añadirla.

<figure><img src="../../.gitbook/assets/image (1532).png" alt=""><figcaption></figcaption></figure>
