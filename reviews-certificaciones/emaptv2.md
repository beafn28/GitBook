# eMAPTv2

### Contexto y motivación

Decidí preparar y afrontar la **eMAPT (eLearn Mobile Application Penetration Tester)** porque quería **salir de mi zona de confort**. Venía de pentesting web, Active Directory y CTFs, y el **mobile app pentesting** es claramente una de las áreas más demandadas hoy en AppSec.

El objetivo no era solo sumar una certificación, sino **entender cómo se rompen aplicaciones móviles reales**, tanto en Android como en iOS, y validar ese conocimiento con un examen práctico.

### Qué cubre realmente la eMAPT

La certificación se centra en **pentesting de aplicaciones móviles Android e iOS**, con un enfoque claro en análisis estático, bypasses y explotación de malas prácticas comunes.

Durante la preparación y el examen se trabajan, entre otros, los siguientes puntos:

* **Análisis estático y reverse engineering** de aplicaciones Android e iOS
* Uso de **jadx-gui** para análisis de código y binarios
* **Bypass de root / jailbreak detection**
* **Bypass de SSL pinning** usando **Frida**
* Identificación de:
  * componentes Android inseguros
  * configuraciones incorrectas en el `AndroidManifest`
* Evaluación de:
  * malas prácticas de almacenamiento
  * fallos de lógica de negocio
* Explotación de **debilidades habituales en aplicaciones móviles reales**

### Formato del examen (actualizado)

* Duración: **12 horas**
* Sin proctoring
* Entorno Linux preconfigurado (web-based)
* Aplicaciones entregadas: **2 IPA**
* El objetivo es:
  * analizar las apps
  * identificar vulnerabilidades
  * responder preguntas (formato test o escribir contraseña u otra cosa)

### Preparación seguida

La preparación combinó **teoría + práctica:**

#### Teoría

* Módulos de HTB sobre Android e IOS
* Estudio específico de:
  * SSL pinning
  * Frida
  * root / jailbreak detection

#### Práctica

* **Mobile Hacking Labs**
* Análisis de múltiples apps vulnerables
* Revisión manual de decenas de `AndroidManifest.xml`

Aquí está la clave: **sin práctica, no se aprueba**.\
El examen cas

### Experiencia durante el examen

El examen no me parece largo para las horas que se dan pero bueno está equilibrado:

* Android ocupa buena parte del tiempo inicial
* En el análisis dinámico requiere cambiar de mentalidad pero da tiempo suficiente
* El entorno está listo para trabajar y las herramientas necesarias
* Hay margen para revisar y validar hallazgos

### Puntos positivos

* Certificación **muy práctica**
* Buen equilibrio Android / iOS
* Examen realista, no artificial
* Refuerza muchísimo:
  * análisis estático
  * análisis dinámico
* Muy alineada con el trabajo real en mobile security

### Aspectos mejorables

Aunque la experiencia general ha sido positiva, hay dos puntos que, en mi opinión, se pueden mejorar:

* El **entorno basado en Guacamole** no es el más cómodo para largas sesiones
* **No hay informe final**, lo cual resta valor desde un punto de vista profesional (reporting es parte clave del pentesting)

No son bloqueantes, pero sí mejorables.

### ¿Para quién es esta certificación?

La eMAPT es ideal si:

* Ya tienes base en seguridad ofensiva
* Quieres entrar en **mobile application security**
* Buscas algo **práctico y reconocido**
* Te interesa AppSec más allá de web

No es para empezar desde cero absoluto, pero **sí para especializarte**.

### Conclusión

La **eMAPT** es una certificación **exigente, práctica y bien enfocada**.\
No te enseña solo herramientas: te enseña **a pensar como un auditor de aplicaciones móviles**.

Si se complementa con:

* laboratorios de mobile hacking
* módulos de Android e iOS en HTB
* práctica constante

…es una muy buena validación de conocimientos reales en **Mobile AppSec**.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-12-23 a las 20.18.22.png" alt=""><figcaption></figcaption></figure>
