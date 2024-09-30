# Nivel 6-11

### NIVEL 6

Ingresamos las credenciales para el nivel 6.

* **Usuario:** `natas6`
* **Contraseña:** `0RoJwHdSKWFTYR5WuiAewauSuNaBXned`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 6**. El mensaje indica que hay que ingresar una clave secreta. Por lo que también indagaremos por el código fuente.

<figure><img src="../../../.gitbook/assets/image (383).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (384).png" alt=""><figcaption></figcaption></figure>

Parece que el **PHP** está incluyendo un enlace a un archivo almacenado en la página web `/includes/secret.inc`. Vamos a añadir eso al final de nuestra **URL**, de la siguiente manera: `http://natas6.natas.labs.overthewire.org/includes/secret.inc`. Deberíamos llegar a una página en blanco, así que veamos el código fuente de la página.

&#x20;![](<../../../.gitbook/assets/image (385).png>)

Otro script **PHP**, con el código secreto que necesitamos. Volvamos a la página de inicio para introducirlo.

<figure><img src="../../../.gitbook/assets/image (386).png" alt=""><figcaption></figcaption></figure>

### NIVEL 7

Ingresamos las credenciales para el nivel 7.

* **Usuario:** `natas7`
* **Contraseña:** `bmg8SvU1LizuWjx3y7xkNERkHxGre0GS`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 7**. Nos encontramos que hay dos apartados **Home** y **About** pero cuando se pulsa en ellos no se ve nada por lo que indagaremos en el código fuente.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-18 175542.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (387).png" alt=""><figcaption></figcaption></figure>

El comentario en **HTML** sugiere que podemos obtener la contraseña desde el archivo `etc/natas_webpass/natas8`. Basándonos en esta pista, parece que estamos tratando con un Ataque de Traversal de Directorios.

Cuando hacemos clic en **Home**, la **URL** debería ser `http://natas7.natas.labs.overthewire.org/index.php?page=home`. Vamos a reemplazar "home" con `/etc/natas_webpass/natas8`. La **URL** final debería ser: `http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8`.

<figure><img src="../../../.gitbook/assets/image (388).png" alt=""><figcaption></figcaption></figure>

### NIVEL 8

Ingresamos las credenciales para el nivel 8.

* **Usuario:** `natas8`
* **Contraseña:** `xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 8**. El mensaje indica que hay que ingresar una clave secreta. Por lo que también indagaremos por el código fuente.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2024-08-18 180218.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (389).png" alt=""><figcaption></figcaption></figure>

Parece que el código secreto que necesitamos está encriptado. Al revisar el código **PHP**, vemos que el **"secreto"** ingresado se convierte de binario a hexadecimal, se invierte y luego se codifica en **base64**.

Para obtener el **"secreto"**, debemos realizar el proceso inverso. Empecemos abriendo la consola e iniciando **PHP** con el comando `php -a`. Luego, descodificamos la cadena en **base64**, revertimos el contenido y convertimos el hexadecimal de nuevo a binario para obtener la clave secreta.

<figure><img src="../../../.gitbook/assets/image (390).png" alt=""><figcaption></figcaption></figure>

Tras obtener el **"secreto"** desencriptado lo ingresamos en la página inicial del nivel.

<figure><img src="../../../.gitbook/assets/image (391).png" alt=""><figcaption></figcaption></figure>

### NIVEL 9

Ingresamos las credenciales para el nivel 9.

* **Usuario:** `natas9`
* **Contraseña:** `ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 9**. Nos aparece un buscador de palabras que tengamos en nuestra entrada por lo que miramos el código fuente para más información.

<figure><img src="../../../.gitbook/assets/image (392).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>

En el script **PHP**, la forma en que se utiliza la palabra **“key”** sugiere que podríamos insertar código arbitrario. A continuación, te explico cómo:

1.  **Ejemplo de Comando:** Si introducimos la palabra **“password”**, el comando `passthru` en el script **PHP** se ejecutará como:

    ```bash
    grep -i password dictionary.txt
    ```

    Dado que la clave se encapsula entre comillas y no hay filtrado de entrada, podemos insertar caracteres especiales.
2.  **Inyección de Comando:** Podemos aprovechar el separador de comandos `;` para ejecutar múltiples comandos en una sola línea. Además, podemos usar el símbolo `#` para comentar el resto de la línea.

    Por ejemplo, si introducimos el siguiente texto en el campo de entrada:

    ```bash
    ; cat /etc/natas_webpass/natas10 #
    ```

    El comando `passthru` se ejecutará de la siguiente manera:

    ```bash
    grep -i ; cat /etc/natas_webpass/natas10 #
    ```

    Aquí, el símbolo `#` comentará el resto del texto después de `cat /etc/natas_webpass/natas10`, eliminando el archivo `dictionary.txt` de la ejecución.

<figure><img src="../../../.gitbook/assets/image (394).png" alt=""><figcaption></figcaption></figure>

### NIVEL 10

Ingresamos las credenciales para el nivel 10.

* **Usuario:** `natas10`
* **Contraseña:** `t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 10**. Nos aparece un buscador de palabras pero filtrando ciertos caracteres por razones de seguridad que tengamos en nuestra entrada por lo que miramos el código fuente para más información.

<figure><img src="../../../.gitbook/assets/image (395).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (396).png" alt=""><figcaption></figcaption></figure>

Parece que el script es el mismo que en el nivel 9, pero esta vez están filtrando los comandos `;` y `&`.

Sin embargo, aún no han corregido cómo el script maneja la entrada para **“key”**. Por lo tanto, podemos explotar esta vulnerabilidad de manera similar a como lo hicimos en el nivel 9, pero en lugar de usar comandos, usaremos expresiones regulares.

Para ello, podemos ingresar la siguiente entrada en el campo de consulta:

```bash
.* /etc/natas_webpass/natas11 #
```

#### Explicación:

* `.*`: Este patrón indica a `grep` que coincida con cualquier secuencia de caracteres, ignorando mayúsculas y minúsculas.
* `/etc/natas_webpass/natas11`: Este es el archivo que queremos leer.
* `#`: Este símbolo comenta el resto del comando, asegurando que `dictionary.txt` no se procese, evitando errores.

Así, el comando `passthru` se ejecutará como:

```bash
grep -i .* /etc/natas_webpass/natas11 #
```

Esto permite que `grep` lea el contenido de `/etc/natas_webpass/natas11` mientras ignora `dictionary.txt`.

<figure><img src="../../../.gitbook/assets/image (397).png" alt=""><figcaption></figcaption></figure>

### NIVEL 11

Ingresamos las credenciales para el nivel 11.

* **Usuario:** `natas11`
* **Contraseña:** `UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk`

Tras iniciar sesión, nos encontramos con la página de inicio del **Nivel 11**. Al parecer se nos muestra un mensaje en la que las cookies andan encriptadas por lo que veremos el código fuente para mas información.

<figure><img src="../../../.gitbook/assets/image (398).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (399).png" alt=""><figcaption></figcaption></figure>

Parece que las cookies están protegidas con cifrado **XOR**. Vamos a obtener la **cookie** cifrada con XOR que está utilizando el sitio. Iniciamos **Burp Suite**, intercepta el paquete y deberías obtener lo siguiente:

<figure><img src="../../../.gitbook/assets/image (400).png" alt=""><figcaption></figcaption></figure>

Si conoces cómo funciona un cifrado XOR, recordarás que se cumple la relación: A XOR B = C.

En este caso, sería: **Datos\_Originales XOR CLAVE = Datos\_Cifrados**.

Para obtener la clave, hacemos lo siguiente: Datos\_Originales XOR Datos\_Cifrados = CLAVE, dado que ya contamos con los Datos\_Originales y los Datos\_Cifrados.

Vamos a usar **PHP** y escribir el siguiente script para descifrar la clave.

<figure><img src="../../../.gitbook/assets/image (401).png" alt=""><figcaption></figcaption></figure>

El script anterior se ha diseñado utilizando la función `xor_encrypt()` del código original. Hemos configurado la cookie (después de decodificarla desde **base64**) como texto sin formato, y hemos usado como clave la versión codificada en **JSON** de la matriz predeterminada que ya conocemos.

Dado que estamos trabajando con dos valores ya conocidos, la salida de la función debe revelar la clave utilizada en el cifrado. Como se mencionó anteriormente, debido a que la clave es más corta que el texto sin formato, se repite. A partir de esto, podemos deducir que la clave es `eDWo`.

Al conocer la clave utilizada en este cifrado, podemos crear una nueva matriz, cifrarla con la misma clave y obtener un resultado que siga siendo válido. En este caso, nuestro objetivo es modificar la matriz original, cambiando el valor de `showpassword` a `yes`, luego cifrarla de la misma manera para generar una nueva cookie válida.

<figure><img src="../../../.gitbook/assets/image (402).png" alt=""><figcaption></figcaption></figure>

Esto generará la siguiente cadena: `HmYkBwozJw4WNyAAFyB1VUc9MhxHaHUNAic4Awo2dVVHZzEJAyIxCUc5`, que es la versión codificada del array modificado. El siguiente paso es configurar esta cadena como una nueva cookie.

Para hacerlo, accede al sitio web del desafío, abre la consola de herramientas para desarrolladores y escribe el siguiente comando:

```javascript
document.cookie = "data=HmYkBwozJw4WNyAAFyB1VUc9MhxHaHUNAic4Awo2dVVHZzEJAyIxCUc5";
```

Esto establecerá la nueva cookie, y al actualizar la página, deberías ver el resultado esperado.

<figure><img src="../../../.gitbook/assets/image (403).png" alt=""><figcaption></figcaption></figure>
