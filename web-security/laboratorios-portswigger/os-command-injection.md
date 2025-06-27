# OS command injection

## Lab: OS command injection, simple case

### Enunciado

Este laboratorio contiene una vulnerabilidad de **inyección de comandos del sistema operativo (OS command injection)** en el verificador de stock de productos.

La aplicación ejecuta un **comando de terminal** que incluye el ID del producto y el ID de la tienda proporcionados por el usuario, y devuelve la salida cruda del comando en la respuesta. Ejecutar el comando `whoami` para obtener el nombre del usuario actual del sistema.

### Resolucón

Interceptamos la petición de stock de productos tras lo que nos dice el enunciado y la mandamos a **Repeater**.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Para ver si es vulnerable a inyección de comandos metemos una comilla simple tras el ID.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tras ver el error que nos da está claro que sí lo es para ello realizamos esta petición.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-22 215543.png" alt=""><figcaption></figcaption></figure>

También otra opción es poner lo siguiente y enviarlo:

```
productId=1&storeId=3;whoami
productId=1&storeId=3|whoami
productId=1&storeId=3%26%26whoami
```

Ya sabemos que el usuario es **peter-VlOzEk.**
