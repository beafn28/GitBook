# Shells & Payloads



{% file src="../.gitbook/assets/Shells_Payloads_Module_Cheat_Sheet.pdf" %}

## Los Shells Nos Conectan, los Payloads Nos Entregan Shells

Un **shell** es un programa que proporciona a un usuario una interfaz para introducir instrucciones en el sistema y ver la salida en formato de texto (Bash, Zsh, cmd y PowerShell, por ejemplo). Como profesionales de la seguridad de la información y testers de penetración, un shell a menudo es el resultado de explotar una vulnerabilidad o eludir medidas de seguridad para obtener acceso interactivo a un host. Es común escuchar las siguientes frases en conversaciones sobre una sesión de prueba o práctica reciente:

* "¡Atrapé un shell!"
* "¡Hice popping a un shell!"
* "¡Caí en un shell!"
* "¡Estoy dentro!"

Estas frases suelen significar que la persona ha explotado exitosamente una vulnerabilidad en un sistema y ha podido obtener control remoto del **shell** del sistema operativo del objetivo. Este es un objetivo común para los **pentesters** al intentar acceder a una máquina vulnerable. La mayor parte de este módulo se centrará en lo que ocurre después de la enumeración y la identificación de exploits prometedores.

### ¿Por qué Obtener un Shell?

Recuerda que un **shell** nos da acceso directo al **sistema operativo**, comandos del sistema y al **sistema de archivos**. Si logramos el acceso, podemos empezar a enumerar el sistema en busca de vectores que nos permitan escalar privilegios, pivotar, transferir archivos, y más. Sin una sesión de shell, nuestras opciones para avanzar en una máquina objetivo son muy limitadas.

Además, establecer un **shell** nos permite mantener la **persistencia** en el sistema, dándonos más tiempo para trabajar. Facilita el uso de nuestras herramientas de ataque, la exfiltración de datos, la recopilación, almacenamiento y documentación de todos los detalles de nuestro ataque, como veremos en las siguientes demostraciones.

Es importante señalar que establecer un shell significa casi siempre que estamos accediendo a la **CLI (Interfaz de Línea de Comandos)** del sistema operativo, lo que puede hacernos más difíciles de detectar en comparación con acceder remotamente a una **shell gráfica** mediante VNC o RDP. Otro beneficio significativo de dominar las interfaces de línea de comandos es que son más difíciles de detectar que los shells gráficos, más rápidas para navegar por el sistema operativo y más fáciles de automatizar.

En este módulo, examinaremos los shells desde las siguientes perspectivas:

| Perspectiva                 | Descripción                                                                                                                                                                                                                                                                                                                                                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Computación**             | El entorno de usuario basado en texto que se utiliza para administrar tareas y enviar instrucciones en un PC. Piensa en Bash, Zsh, cmd y PowerShell.                                                                                                                                                                                                                                                                |
| **Explotación y Seguridad** | Un shell es a menudo el resultado de explotar una vulnerabilidad o eludir medidas de seguridad para obtener acceso interactivo a un host. Un ejemplo sería activar **EternalBlue** en un host Windows para acceder remotamente al **cmd-prompt** en el sistema.                                                                                                                                                     |
| **Web**                     | Un **web shell** es similar a un shell estándar, pero explota una vulnerabilidad (a menudo la capacidad de cargar un archivo o script) que proporciona al atacante una manera de emitir instrucciones, leer y acceder a archivos, y potencialmente realizar acciones destructivas en el host subyacente. El control del web shell se realiza frecuentemente llamando al script dentro de una ventana del navegador. |

### Los Payloads nos Entregan Shells

Dentro de la industria de TI, un **payload** puede definirse de varias formas:

* **Redes**: La parte de datos encapsulada de un paquete que atraviesa las redes informáticas modernas.
* **Computación Básica**: Un payload es la porción de un conjunto de instrucciones que define la acción a realizar. La información de encabezados y protocolo se elimina.
* **Programación**: La parte de datos referenciada o transportada por la instrucción del lenguaje de programación.
* **Explotación y Seguridad**: Un **payload** es código diseñado con la intención de explotar una vulnerabilidad en un sistema informático. El término "payload" puede describir varios tipos de malware, incluidos pero no limitados a ransomware.

En este módulo, trabajaremos con muchos tipos diferentes de **payloads** y métodos de entrega dentro del contexto de otorgarnos acceso a un host y establecer sesiones de shell remoto en sistemas vulnerables.

## Preparación para el Compromiso de CAT5 Security

Somos un tester de penetración trabajando para **CAT5 Security**, y estamos preparándonos para realizar un compromiso para nuestro cliente, **Inlanefreight**. Los miembros más experimentados del equipo de CAT5 quieren evaluar nuestras habilidades con **shells** y **payloads** antes de incluirnos en el compromiso real.

La evaluación final de este módulo consiste en una serie de desafíos que **CAT5 Security** ha creado para probar nuestras habilidades. Completar el desafío significa que hemos pasado la evaluación.

### Conceptos Básicos de Shell

Replicar la capacidad de obtener un shell **bind** y **reverse**:

1. **Shell bind en un host Linux**.
2. **Shell reverse en un host Windows**.

### Conceptos Básicos de Payload

1. **Demostrar el lanzamiento de un payload desde Metasploit Framework (MSF)**.
2. **Demostrar la búsqueda y construcción de un payload desde PoC en ExploitDB**.
3. **Demostrar conocimiento en la creación de payloads**.

### Obtener un Shell en Windows

Usando los resultados de la recon proporcionada, crear o utilizar un **payload** que explote el host y proporcione un **shell** de vuelta.

### Obtener un Shell en Linux

Usando los resultados de la recon proporcionada, crear o utilizar un **payload** que explote el host y establezca una sesión de shell.

### Implementar un Web Shell

1. Demostrar conocimiento de **web shells** y aplicaciones web comunes, identificando una aplicación web común y su lenguaje correspondiente.
2. Usar los resultados de la recon proporcionada para desplegar un **payload** que proporcione acceso a un shell desde el navegador.

### Detectar un Shell o Payload

Detectar la presencia de un **payload** o un shell interactivo en un host mediante el análisis de la información relevante proporcionada.
