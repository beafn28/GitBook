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

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Reflected XSS

#### Preguntas

To get the flag, use the same payload we used above, but change its JavaScript code to show the cookie instead of showing the url.

```
<script>alert(document.cookie)</script>
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### XSS Discovery

#### Automated Discovery

```
git clone https://github.com/s0md3v/XSStrike.git
cd XSStrike
pip install -r requirements.txt
python xsstrike.py
```

