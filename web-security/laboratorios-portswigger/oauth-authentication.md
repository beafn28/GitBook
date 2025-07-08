# OAuth authentication

## Lab: Authentication bypass via OAuth implicit flow

### Enunciado

Este laboratorio utiliza un servicio OAuth para permitir que los usuarios inicien sesión con su cuenta de redes sociales. Una validación incorrecta en la aplicación cliente hace posible que un atacante inicie sesión en la cuenta de otros usuarios sin conocer su contraseña. Inicia sesión en la cuenta de Carlos. Su dirección de correo electrónico es: `carlos@carlos-montoya.net`

Puedes iniciar sesión con tu propia cuenta de redes sociales usando las siguientes credenciales:

* **Usuario:** `wiener`
* **Contraseña:** `peter`

### Resolución

Nos logueamos y nos autenticamos.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-07 221647.png" alt=""><figcaption></figcaption></figure>

En este flujo OAuth, el access token se envía al cliente a través del navegador como fragmento de URL, y el cliente lo recoge con JavaScript.

Para mantener la sesión tras cerrar la página, la aplicación suele enviar el ID de usuario y el access token al servidor mediante una petición POST. El servidor entonces asigna una cookie de sesión.

Pero el problema es que el servidor confía en estos datos sin validarlos bien, ya que no tiene una contraseña para comparar.

En el "implicit flow", esta petición POST es visible y manipulable en el navegador. Si la aplicación no valida que el access token corresponde al usuario indicado, un atacante puede modificar el parámetro email y suplantar a otro usuario.

Los pasos del ataque son cerrar sesión, iniciar sesión de nuevo, y en la petición POST a **/authenticate** cambiar el parámetro `email` a `carlos@carlos-montoya.net`.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-07 221844.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-07 221910.png" alt=""><figcaption></figcaption></figure>
