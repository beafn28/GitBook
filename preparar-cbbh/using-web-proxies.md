# Using Web Proxies

{% file src="../.gitbook/assets/Using_Web_Proxies_Module_Cheat_Sheet.pdf" %}

## Web Proxy

### Intercepting Web Requests

#### Preguntas

**Try intercepting the ping request on the server shown above, and change the post data similarly to what we did in this section. Change the command to read 'flag.txt'**

<figure><img src="../.gitbook/assets/image (1680).png" alt=""><figcaption></figcaption></figure>

### Repeating Requests

#### Preguntas

**Try using request repeating to be able to quickly test commands. With that, try looking for the other flag.**

El comando que hay que usar para buscar otra flag es:

```
find / -type f -name 'flag.txt' 2>/dev/null
```

Enviamos la petición con ese comando pero urlcodeado.

<figure><img src="../.gitbook/assets/image (1682).png" alt=""><figcaption></figcaption></figure>

Como podemos ver nos sale la ruta de la otra flag, ya podemos ver su contenido.

```
cat /flag.txt
```

<figure><img src="../.gitbook/assets/image (1684).png" alt=""><figcaption></figcaption></figure>

### Encoding/Decoding

#### Preguntas

**The string found in the attached file has been encoded several times with various encoders. Try to use the decoding tools we discussed to decode it and get the flag.**

Decoficamos 4 veces por base64 y una por URL.

<figure><img src="../.gitbook/assets/image (1685).png" alt=""><figcaption></figcaption></figure>

### Proxying Tools

Interceptar y analizar el tráfico HTTP/HTTPS de herramientas de línea de comando o aplicaciones usando un proxy web como **Burp Suite** o **OWASP ZAP**.

#### Métodos principales

1. **Configurar el proxy en la herramienta**
   * Ejemplo: `http://127.0.0.1:8080`
   * Cada herramienta tiene su propio método para definir proxy.
2. **Proxychains (Linux)**
   *   Editar `/etc/proxychains.conf`:

       ```bash
       #socks4 127.0.0.1 9050
       http 127.0.0.1 8080
       ```
   * Activar `quiet_mode`.
   *   Ejecutar:

       ```bash
       proxychains curl http://SERVER_IP:PORT
       ```
3. **Nmap**
   *   Uso directo:

       ```bash
       nmap --proxies http://127.0.0.1:8080 SERVER_IP -pPORT -Pn -sC
       ```
   * Si falla, usar con `proxychains`.
4. **Metasploit**
   *   Configurar dentro del módulo:

       ```bash
       set PROXIES HTTP:127.0.0.1:8080
       set RHOST SERVER_IP
       set RPORT PORT
       run
       ```

{% hint style="warning" %}
El uso de proxy puede **ralentizar** las herramientas, por lo que se recomienda activarlo solo para análisis. Sirve para depuración, repetir y modificar solicitudes, y hacer pentesting de aplicaciones web.
{% endhint %}

#### Preguntas

**Try running 'auxiliary/scanner/http/http\_put' in Metasploit on any website, while routing the traffic through Burp. Once you view the requests sent, what is the last line in the request?**

<pre><code><strong>use auxiliary/scanner/http/http_put
</strong>set Proxies HTTP:127.0.0.1:8080
</code></pre>

Interceptando con Burp, la respuesta es: msf test file.

## Web Fuzzer

### Burp Intruder

#### Preguntas

**Use Burp Intruder to fuzz for '.html' files under the /admin directory, to find a file containing the flag.**

Configuramos en el **Intruder** el ataque con el diccionario **common.txt** de **SecLists**.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Empezamos ataque.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```
 curl http://94.237.60.55:33802/admin/2010.html
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### ZAP Fuzzer

#### Preguntas

**The directory we found above sets the cookie to the md5 hash of the username, as we can see the md5 cookie in the request for the (guest) user. Visit '/skills/' to get a request with a cookie, then try to use ZAP Fuzzer to fuzz the cookie for different md5 hashed usernames to get the flag. Use the "top-usernames-shortlist.txt" wordlist from Seclists.**

Interceptamos la petición.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Decodificamos la cookie.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Realizamos el Fuzzing con los usuarios hasheados.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

