# CRTA

### Contexto y motivación

Me animé a realizar el **CRTA (Certified Red Team Analyst)** principalmente por **curiosidad técnica**.\
Ya contaba con certificaciones previas como **eCPPT** y **eWPTx**, por lo que no partía desde cero ni buscaba una introducción básica a la seguridad ofensiva. Aun así, el enfoque del CRTA llamó mi atención por dos motivos claros:

* **Precio muy accesible** (especialmente en comparación con otras certificaciones de red team).
* Un **examen 100% práctico**, centrado en Active Directory, pero sin aislarlo del mundo real.

Y aquí está el matiz importante: **no es solo AD “puro”**, sino una mezcla razonable de **técnicas web + pivoting + AD**, algo que se agradece.

### ¿Qué tipo de examen es?

* Examen **hands-on de 6 horas**
* Entorno realista tipo corporativo
* Sin preguntas tipo test
* Basado en **enumerar, explotar, pivotar, moverse lateralmente y escalar**

No es un examen para correr como pollo sin cabeza.\
Es un examen para **pensar**, enumerar bien y encadenar accesos.

### Qué se aprende (y se refuerza)

Aunque no inventa nada nuevo, el CRTA refuerza conceptos clave que **sí se usan en el mundo real**:

#### Active Directory

* Enumeración de dominio
* Kerberos abuse (AS-REP Roasting, Kerberoasting)
* Abuso de configuraciones débiles
* Movimiento lateral
* Escalada de privilegios en entornos Windows

#### Pivoting y networking ofensivo

* Uso de **Ligolo-ng**
* Túneles, rutas y acceso a redes internas

#### Toque web (el detalle que suma puntos)

* No todo es AD desde el minuto uno
* Puede haber **vectores web iniciales**
* Saber pasar de web → sistema → dominio es clave

Esta combinación hace que el examen sea **más completo** que otros puramente enfocados a AD.

### Nivel de dificultad (opinión honesta)

* **No es difícil si ya tienes base**
* **Sí puede ser traicionero** si no enumeras con calma
* El laboratorio suele ser incluso más exigente que el examen

Si vienes de:

* CTFs
* HTB
* Certificaciones prácticas previas

…el examen es **totalmente abordable**.

Si vienes solo de teoría, probablemente sufras.

### Preparación recomendada

En mi caso, **no partía de cero**, y eso marca la diferencia.\
La preparación fue más de **consolidación y curiosidad técnica** que de aprendizaje desde cero.

Recomendaciones claras:

* Dominar **enumeración en AD**
* Tener soltura con:
  * Ligolo-ng
  * BloodHound
  * Herramientas clásicas de Windows y Linux
* No depender solo de una herramienta
* Entender **por qué funciona cada ataque**, no solo el comando.

### Lo que hay que tener claro

* Es una certificación "begginer"
* No te convierte en un red teamer&#x20;
* **Sí es un muy buen refuerzo práctico**
* Especialmente útil como puente entre CTFs y certificaciones&#x20;

### Conclusión

El CRTA es una certificación que **vale más de lo que cuesta**.\
No por ser perfecta, sino porque **enseña lo correcto para su nivel**.

Es un examen donde:

* Aprendes
* Refuerzas conceptos reales
* Practicas pivoting y AD de forma coherente
* Y sales con más criterio técnico

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
