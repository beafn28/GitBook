# Authentication

## Lab: Username enumeration via different responses

### Enunciado

Este laboratorio es vulnerable a la enumeración de nombres de usuario y ataques de fuerza bruta de contraseñas. Cuenta con una cuenta que tiene un nombre de usuario y una contraseña predecibles, los cuales se pueden encontrar en las siguientes listas de palabras:

* Candidatos a nombres de usuario
* Candidatos a contraseñas

Para resolver el laboratorio, **enumera un nombre de usuario válido**, luego realiza un **ataque de fuerza bruta para descubrir su contraseña**, y finalmente **accede a la página de cuenta de ese usuario**.

### Resolución

Interceptamos la petición cuando ingresamos unas credenciales. Seleccionamos el parámetro para realizar un ataque de fuerza bruta con los usuarios posibles proporcionados.

<figure><img src="../../.gitbook/assets/image (1360).png" alt=""><figcaption></figcaption></figure>

Tras realizar el ataque vemos por la longitud que hay un usuario en  el que la respuesta cambia.

<figure><img src="../../.gitbook/assets/image (1359).png" alt=""><figcaption></figcaption></figure>

Sabiendo esto mandamos otra vez al **Intruder** seleccionando el parámetro de **password** para realizar el ataque con las contraseñas proporcionadas.

<figure><img src="../../.gitbook/assets/image (1361).png" alt=""><figcaption></figcaption></figure>

Realizamos el ataque y nos damos cuenta que con una contraseña tenemos una longitud distinta.

<figure><img src="../../.gitbook/assets/image (1358).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Se podría haber realizado el ataque en una instancia pero realiza más ruido en un servidor.
{% endhint %}

Nos logueamos con esas credenciales.

<figure><img src="../../.gitbook/assets/image (1362).png" alt=""><figcaption></figcaption></figure>

## Lab: 2FA simple bypass

### Enunciado

Este laboratorio permite **evadir la autenticación en dos factores (2FA)**. Ya has obtenido un nombre de usuario y una contraseña válidos, pero **no tienes acceso al código de verificación 2FA del usuario**.

Para resolver el laboratorio, debes **acceder a la página de cuenta de Carlos**.

#### Credenciales disponibles:

* **Tus credenciales (usuario normal):** `wiener:peter`
* **Credenciales de la víctima (Carlos):** `carlos:montoya`

### Resolución

Nos logueamos con las credenciales dadas y le damos a Email client.

<figure><img src="../../.gitbook/assets/image (1363).png" alt=""><figcaption></figcaption></figure>

Insertamos el código. Descubrimos que hay un paso intermedio en que nos podemos ahorrar de /login y /login2. Nos logueamos con las credenciales de la víctima pero antes de eso interceptamos y como vimos para saltarnos **/login2** cambiamos por **/my-account** y enviamos.

<figure><img src="../../.gitbook/assets/image (1364).png" alt=""><figcaption></figcaption></figure>

Se cambia y se envía.

```
GET /my-account
```

Nos hemos saltado la doble autenticación.

<figure><img src="../../.gitbook/assets/image (1365).png" alt=""><figcaption></figcaption></figure>

## Lab: Password reset broken logic

### Enunciado

Este laboratorio tiene una vulnerabilidad en la funcionalidad de **restablecimiento de contraseña**

Datos disponibles:

* **Tus credenciales (usuario común):** `wiener:peter`
* **Nombre de usuario de la víctima:** `carlos`

### Resolución

Pulsamos en que se no ha olvidado la contraseña con el usuario wiener y se nos envía un correo que al pulsar podemos restablecer contraseña.

Vemos que en la petición de olvidar contraseña hay un token pero claro ese token normalmente tiene un tiempo límite debe cambiar por seguridad. Pensamos y si este token puede usarse para otro usuario por lo que lo comprobamos.

{% hint style="info" %}
Antes tenía de contraseña test y la he cambiado a hola
{% endhint %}

<figure><img src="../../.gitbook/assets/image (1366).png" alt=""><figcaption></figcaption></figure>

Ahora nos volvemos a loguear con esas credenciales nuevas y entramos por lo que no caduca. Cogemos esa petición de cambiar contraseña pero con Carlos.

<figure><img src="../../.gitbook/assets/image (1367).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con las credenciales de Carlos y su contraseña cambiada.

<figure><img src="../../.gitbook/assets/image (1368).png" alt=""><figcaption></figcaption></figure>
