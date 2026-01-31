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

    <figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>


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

