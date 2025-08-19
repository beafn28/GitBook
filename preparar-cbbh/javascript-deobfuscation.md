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

