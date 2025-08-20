# JavaScript Deobfuscation

{% file src="../.gitbook/assets/Javascript_Deobfuscation_Module_Cheat_Sheet.pdf" %}

## Source Code

#### Preguntas

**Repeat what you learned in this section, and you should find a secret flag, what is it?**

```
curl http://94.237.50.221:31522
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## **Obfuscation**

### Basic Obfuscation

[JSConsole](https://jsconsole.com/)

[javascript-minifier](https://javascript-minifier.com/)

> La minimización se puede realizar a muchos otros lenguajes.

#### Empaquetado

[BeautifyTools](http://beautifytools.com/javascript-obfuscator.php)

```
eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('5.4(\'3 2 1 0\');',6,6,'Module|Deobfuscation|JavaScript|HTB|log|console'.split('|'),0,{}))
```

Se suele intentar convertir todas las palabras y símbolos del código en una lista o diccionario y luego consultarlos mediante la `(p,a,c,k,e,d)`función para reconstruir el código original durante la ejecución. Esto `(p,a,c,k,e,d)`puede variar según el empaquetador. Sin embargo, suele contener un orden específico en el que se empaquetaron las palabras y símbolos del código original para saber cómo ordenarlos durante la ejecución.

### Advanced Obfuscation

{% embed url="https://utf-8.jp/public/jjencode.html" %}

{% embed url="https://utf-8.jp/public/aaencode.html" %}

### Deobfuscation

{% embed url="https://prettier.io/playground/" %}

{% embed url="https://beautifier.io/" %}

{% embed url="https://matthewfl.com/unPacker.html" %}

#### Preguntas

**Using what you learned in this section, try to deobfuscate 'secret.js' in order to get the content of the flag. What is the flag?**

<figure><img src="../.gitbook/assets/image (1687).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1688).png" alt=""><figcaption></figcaption></figure>

## Deobfuscation Examples

### HTTP Requests

* **GET simple**

```bash
curl http://SERVER_IP:PORT/
```

* **POST vacío**

```bash
curl -s http://SERVER_IP:PORT/serial.php -X POST
```

* **POST con datos**

```bash
curl -s http://SERVER_IP:PORT/serial.php -X POST -d "param1=sample"
```

#### Preguntas

**Try applying what you learned in this section by sending a 'POST' request to '/serial.php'. What is the response you get?**

```
curl -s http://94.237.48.12:56750/serial.php -X POST
```

<figure><img src="../.gitbook/assets/image (1689).png" alt=""><figcaption></figcaption></figure>

### Decoding

#### 1. **Base64**

* Usa solo caracteres alfanuméricos + `+`, `/` y `=`.
* Siempre múltiplos de 4 (relleno con `=`).
*   **Codificar:**

    ```bash
    echo "texto" | base64
    ```
*   **Decodificar:**

    ```bash
    echo "cadena_base64" | base64 -d
    ```

#### 2. **Hex**

* Representa cada carácter en su valor **hex ASCII**.
* Solo usa `0-9` y `a-f`.
*   **Codificar:**

    ```bash
    echo "texto" | xxd -p
    ```
*   **Decodificar:**

    ```bash
    echo "hexstring" | xxd -p -r
    ```

#### 3. **Caesar / Rot13**

* Cifrado por sustitución → desplaza letras un número fijo (rot13 = 13 posiciones).
* Mantiene cierta similitud en las palabras.
*   **Codificar/Decodificar (rot13):**

    ```bash
    echo "texto" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
    ```

#### Preguntas

**Using what you learned in this section, determine the type of encoding used in the string you got at previous exercise, and decode it. To get the flag, you can send a 'POST' request to 'serial.php', and set the data as "serial=YOUR\_DECODED\_OUTPUT".**

Tras el resultado de la anterior pregunta del otro apartado vemos que es una cadena base64 por lo que la decodificamos.

```
echo N2gxNV8xNV9hX3MzY3IzN19tMzU1NGcz | base64 -d
```

<figure><img src="../.gitbook/assets/image (1690).png" alt=""><figcaption></figcaption></figure>

Enviamos con el output decodificado.

```
 curl -s http://94.237.48.12:56750/serial.php -X POST -d "serial=7h15_15_a_s3cr37_m3554g3"
```

<figure><img src="../.gitbook/assets/image (1691).png" alt=""><figcaption></figcaption></figure>

## Skills Assessment

#### Preguntas

**Try to study the HTML code of the webpage, and identify used JavaScript code within it. What is the name of the JavaScript file being used?**

<figure><img src="../.gitbook/assets/image (1692).png" alt=""><figcaption></figcaption></figure>

**Once you find the JavaScript code, try to run it to see if it does any interesting functions. Did you get something in return?**

<figure><img src="../.gitbook/assets/image (1693).png" alt=""><figcaption></figcaption></figure>

**As you may have noticed, the JavaScript code is obfuscated. Try applying the skills you learned in this module to deobfuscate the code, and retrieve the 'flag' variable.**

<figure><img src="../.gitbook/assets/image (1694).png" alt=""><figcaption></figcaption></figure>

**Try to Analyze the deobfuscated JavaScript code, and understand its main functionality. Once you do, try to replicate what it's doing to get a secret key. What is the key?**

```
curl -s -X POST http://94.237.57.115:49971/keys.php
```

<figure><img src="../.gitbook/assets/image (1695).png" alt=""><figcaption></figcaption></figure>

**Once you have the secret key, try to decide it's encoding method, and decode it. Then send a 'POST' request to the same previous page with the decoded key as "key=DECODED\_KEY". What is the flag you got?**

```
curl -s http://94.237.57.115:49971/keys.php -X POST -d "key=API_p3n_73571n6_15_fun"
```

<figure><img src="../.gitbook/assets/image (1696).png" alt=""><figcaption></figcaption></figure>
