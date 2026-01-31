# Authentication

## Lab 1: Enumeración de usuarios por respuestas distintas

1. Interceptar la petición.
2. Mandar al Intruder y realizamos un ataque sniper con el diccionario que nos proporcionan.
3. Descubrimos tras el error que un usuario es válido.
4. Realizamos fuerza bruta con las contraseñas sabiendo el usuario.&#x20;
5. Nos autenticamos sabiendo las credenciales.

## Lab 2: Bypass básico de 2FA

1. Nos logueamos y nos pide un pin.
2. Cambiamos el endpoint /login2 a /my-account.

## Lab 3: Lógica rota en reseteo de contraseña

1. Nos logueamos y pulsamos a olvidar contraseña.
2. Interceptamos y nos damos cuenta que un token.
3. No valida el token por lo que podemos cambiar la contraseña a Carlos con ese mismo token.

## Lab 4: Enumeración por respuestas sutilmente distintas

1. Interceptamos y mandamos al Intruder para realizar la fuerza bruta con los diccionarios proporcionados.
2. Damos a Grep para poner la respuesta de error y ver qué petición es distinta.
3. Sacamos el usuario y hacemos lo mismo para la contraseña.

## Lab 5: Enumeración de usuario por tiempo de respuesta

1. Al probar varios intentos de credenciales, nos comenta que lo volvamos a intentar en 30 minutos.
2. [https://gist.github.com/kaimi-/6b3c99538dce9e3d29ad647b325007c1](https://gist.github.com/kaimi-/6b3c99538dce9e3d29ad647b325007c1) X\_FORWARDED\_FOR usar este. Miramos que ya no sale ese error.
3. Si ponemos una contraseña muy larga comprobamos que tarda.
4. Vamos al Intruder con Pitchfork tanto para usuario con el diccionario y la IP en el último octeto.
5. Al sacar el usuario realizamos la fuerza bruta con el diccionario de contraseñas y también seguimos con la IP en el último octeto.

## Lab 6: Protección rota, bloqueo por IP

1. Al probar varios intentos de credenciales, nos comenta que lo volvamos a intentar en 1 minuto.
2. Cada dos credenciales para Carlos tenemos que loguearnos con Wiener.
3.

    <figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>


4. Ahora con las contraseñas hacemos lo siguiente para que cada dos credenciales poner la contraseña de Wiener.
5.

    <figure><img src="../../.gitbook/assets/Captura de pantalla 2026-01-31 a las 12.23.27.png" alt=""><figcaption></figcaption></figure>


6. Teniendo esos dos diccionarios hacemos un ataque con el Intruder un Pitchfork.
7. Nos creamos una Resource Pool personalizado como máximo de peticiones concurrentes sea 1.

## Lab 7: Enumeración de usuario por bloqueo de cuenta

1. Interceptamos para mandar al Intruder para un ataque de tipo Cluster Bomb con los diccionarios proporcionados.
2. Creamos ua REGEX con grep con el mensaje de error.
3. Sabemos tras esto cuál es el usuario y para las contraseñas realizamos un ataque de tipo Sniper para las contraseñas.
4. Esperamos el bloqueo e iniciamos sesión.

## Lab 8: Lógica rota en 2FA

1. Interceptamos para generar un código 2FA válido para Carlos manipulando el parámetro verify.
2. Enviar un código inválido en tu propio flujo de autenticación para reutilizar el endpoint de validación.
3. Mediante Intruder realizar un ataque de fuerza bruta sobre el código de Carlos.
4. Nos cogemos la cookie de sesión para loguear siendo Carlos.

## Lab 9: Fuerza bruta de cookie de sesión persistente

1. Nos logueamos y vemos que tenemos dos cookies de sesión.
2. La cookie de stay logged in es una cadena en base64 en la que es usuario:hash MD5 de la contraseña.
3.

    <figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
4. Sabiendo esto cargamos el diccionario de contraseñas y modificar Add Prefix: carlos:.
5. Con esa cookie que nos salga ya nos logueamos.

## Lab 10: Cracking offline de contraseñas

1. Nos logueamos y vemos que la cookie es usuario:md5(contraseña).
2. A través de XSS en comment podemos obtener la cookie de stay logged in.
3.

    <figure><img src="../../.gitbook/assets/Captura de pantalla 2026-01-31 a las 16.46.16 (1).png" alt=""><figcaption></figcaption></figure>
4. Lo mandamos al exploit y conseguimos la cookie de Carlos con su contraseña que la tenemos que crackear.

## Lab 11: Poisoning en reset de contraseña vía middleware

1. Nos logueamos y al olvidar contraseña se nos genera un token cada vez.
2.

    <figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
3. En el exploit server conseguimos el token de Carlos.

## Lab 12: Fuerza bruta de contraseña en cambio de clave

1. Nos logueamos e interceptamos la petición de cambiar contraseña.
2. No se aplica ningún bloqueo, mantenemos las contraseñas nuevas distintas para realizar fuerza bruta a la contraseña actual.
3. Vamos al Intruder y seleccionamos el mensaje que queremos ver para notar cuál es la contraseña actual.
