# OS command injection

## Lab: OS command injection, simple case

### Enunciado

Este laboratorio contiene una vulnerabilidad de **inyección de comandos del sistema operativo (OS command injection)** en el verificador de stock de productos.

La aplicación ejecuta un **comando de terminal** que incluye el ID del producto y el ID de la tienda proporcionados por el usuario, y devuelve la salida cruda del comando en la respuesta. Ejecutar el comando `whoami` para obtener el nombre del usuario actual del sistema.

### Resolucón

Interceptamos la petición de stock de productos tras lo que nos dice el enunciado y la mandamos a **Repeater**.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Para ver si es vulnerable a inyección de comandos metemos una comilla simple tras el ID.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tras ver el error que nos da está claro que sí lo es para ello realizamos esta petición.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-22 215543.png" alt=""><figcaption></figcaption></figure>

También otra opción es poner lo siguiente y enviarlo:

```
productId=1&storeId=3;whoami
productId=1&storeId=3|whoami
productId=1&storeId=3%26%26whoami
```

Ya sabemos que el usuario es **peter-VlOzEk.**

## Lab: Blind OS command injection with time delays

### Enunciado

Este laboratorio contiene una vulnerabilidad de inyección de comandos del sistema operativo (OS) de tipo _blind_ en la función de comentarios.

La aplicación ejecuta un comando de shell que incluye los datos proporcionados por el usuario. La salida del comando no se devuelve en la respuesta.

Para resolver el laboratorio, explota la vulnerabilidad de inyección de comandos _blind_ para provocar un retraso de 10 segundos.

### Resolución

Primero enviamos un payload de prueba en todos los campos para observar la respuesta del servidor. Vemos que la solicitud es aceptada normalmente (código 200), lo que indica que no hay error aún y no sabemos cuál campo es vulnerable.

<figure><img src="../../.gitbook/assets/image (1605).png" alt=""><figcaption></figcaption></figure>

Luego, inyectamos un payload específico (usando `; sleep 10`) en el campo `email`, intentando provocar un retraso deliberado en la ejecución. La respuesta del servidor devuelve un error 500 ("Internal Server Error") con el mensaje _Could not save_. Esto confirma que el campo `email` está siendo procesado por un comando del sistema operativo en el backend y es vulnerable a inyección de comandos.

<figure><img src="../../.gitbook/assets/image (1606).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1607).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind OS command injection with output redirection

### Enunciado

Este laboratorio contiene una vulnerabilidad de inyección de comandos del sistema operativo (_blind OS command injection_) en la función de comentarios.

La aplicación ejecuta un comando de shell que incluye los datos proporcionados por el usuario. La salida del comando no se devuelve en la respuesta. Sin embargo, puedes usar la redirección de salida para capturar la salida del comando. Hay una carpeta con permisos de escritura en:

```
/var/www/images/
```

La aplicación sirve las imágenes del catálogo de productos desde esta ubicación. Puedes redirigir la salida del comando inyectado a un archivo en esta carpeta y luego usar la URL de carga de imágenes para recuperar el contenido del archivo.

Para resolver el laboratorio, ejecuta el comando `whoami` y recupera la salida.

### Resolución

Primero enviamos la solicitud normal con datos inocuos y obtenemos un 200 OK, confirmando que la app procesa el formulario sin errores.

<figure><img src="../../.gitbook/assets/image (1608).png" alt=""><figcaption></figcaption></figure>

Luego inyectamos el payload con `;whoami >/var/www/images/test.txt` en el campo `email`, para ejecutar el comando en el servidor y redirigir la salida a un archivo en la carpeta accesible `/var/www/images/`. La respuesta del servidor muestra error 500 con “Could not save”, pero sabemos que el comando se ejecutó.

<figure><img src="../../.gitbook/assets/image (1612).png" alt=""><figcaption></figcaption></figure>

Finalmente accedemos a la ruta `/image?filename=test.txt` y podemos ver el contenido del archivo con la respuesta del comando `whoami`, revelando el usuario del sistema (en este caso `peter-s6CKU8`).

<figure><img src="../../.gitbook/assets/image (1609).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1611).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1613).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind OS command injection with out-of-band interaction

### Enunciado

Este laboratorio contiene una vulnerabilidad de inyección de comandos del sistema operativo (_blind OS command injection_) en la función de comentarios.

La aplicación ejecuta un comando de shell que incluye los datos proporcionados por el usuario. El comando se ejecuta de forma asíncrona y no tiene efecto en la respuesta de la aplicación. No es posible redirigir la salida a una ubicación a la que puedas acceder. Sin embargo, puedes desencadenar interacciones _out-of-band_ (OAST) con un dominio externo.

Para resolver el laboratorio, explota la vulnerabilidad de inyección de comandos para forzar una consulta DNS hacia Burp Collaborator.

> Para evitar que la plataforma Academy se use para atacar a terceros, nuestro firewall bloquea las interacciones entre los laboratorios y sistemas externos arbitrarios. Para resolver el laboratorio, debes usar el servidor público por defecto de Burp Collaborator.

### Resolución

Identificamos el campo vulnerable enviando el parámetro `email` con un payload de inyección que ejecuta `nslookup` apuntando a nuestro dominio de Burp Collaborator.

El servidor procesa la solicitud y, aunque la respuesta de la aplicación es un 200 OK sin datos relevantes, en el panel de Burp Collaborator recibimos la interacción DNS con nuestro subdominio único.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-14 162350.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-14 162411.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-14 162426.png" alt=""><figcaption></figcaption></figure>

## Lab: Blind OS command injection with out-of-band data exfiltration

### Enunciado

Este laboratorio contiene una vulnerabilidad de inyección de comandos del sistema operativo (_blind OS command injection_) en la función de comentarios.

La aplicación ejecuta un comando de shell que incluye los datos proporcionados por el usuario. El comando se ejecuta de forma asíncrona y no afecta la respuesta de la aplicación. No es posible redirigir la salida a una ubicación accesible. Sin embargo, puedes desencadenar interacciones _out-of-band_ (OAST) con un dominio externo.

Para resolver el laboratorio, debes ejecutar el comando `whoami` y exfiltrar su salida mediante una consulta DNS hacia Burp Collaborator. Necesitarás ingresar el nombre del usuario actual para completar el laboratorio.

> Para evitar que la plataforma Academy se use para atacar a terceros, nuestro firewall bloquea interacciones entre los laboratorios y sistemas externos arbitrarios. Para resolver el laboratorio, debes usar el servidor público por defecto de Burp Collaborator.

### Resolución

Enviamos la solicitud al endpoint de feedback probando inyección en el campo `email` usando un payload que ejecuta `whoami` y hace un `nslookup` con su salida hacia nuestro subdominio en Burp Collaborator.

Aunque la aplicación devuelve un 200 OK vacío confirmamos la ejecución porque en Burp Collaborator vemos las consultas DNS que contienen el resultado del comando inyectado incluyendo el nombre de usuario del sistema atacado que se resolvió como parte del subdominio.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-14 163123.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-14 163116.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-14 163105.png" alt=""><figcaption></figcaption></figure>
