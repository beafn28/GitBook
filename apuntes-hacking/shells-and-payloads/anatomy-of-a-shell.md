# Anatomy of a Shell

Cada sistema operativo tiene un **shell**, y para interactuar con él, debemos utilizar una aplicación conocida como **emulador de terminal**. Aquí están algunos de los emuladores de terminal más comunes:

| **Emulador de Terminal** | **Sistema Operativo**  |
| ------------------------ | ---------------------- |
| Windows Terminal         | Windows                |
| cmder                    | Windows                |
| PuTTY                    | Windows                |
| kitty                    | Windows, Linux y MacOS |
| Alacritty                | Windows, Linux y MacOS |
| xterm                    | Linux                  |
| GNOME Terminal           | Linux                  |
| MATE Terminal            | Linux                  |
| Konsole                  | Linux                  |
| Terminal                 | MacOS                  |
| iTerm2                   | MacOS                  |

Esta lista no incluye todos los emuladores disponibles, pero destaca algunos de los más conocidos. Muchos de estos emuladores son de código abierto, lo que permite instalarlos en diferentes sistemas operativos. La elección del emulador de terminal adecuado para el trabajo es principalmente una cuestión de preferencia personal y de estilo, basada en los flujos de trabajo que desarrollemos a medida que nos familiarizamos con nuestro sistema operativo.

## Intérpretes de Lenguaje de Comandos

Al igual que un intérprete humano traduce el lenguaje hablado o de señas en tiempo real, un **intérprete de lenguaje de comandos** es un programa que interpreta las instrucciones proporcionadas por el usuario y las envía al sistema operativo para su procesamiento. Entonces, cuando hablamos de interfaces de línea de comandos (CLI), nos referimos a una combinación del sistema operativo, la aplicación de emulador de terminal y el intérprete de lenguaje de comandos.

Entender qué intérprete de lenguaje de comandos está en uso en un sistema nos da una idea de qué comandos y scripts podemos usar. Existen varios intérpretes de lenguaje de comandos que también se llaman **lenguajes de script de shell** o **intérpretes de comandos y scripts**, como se define en las técnicas de ejecución de la matriz **MITRE ATT\&CK**.

## Práctica con Emuladores de Terminal y Shells

Vamos a usar **Parrot OS Pwnbox** para explorar más sobre la anatomía de un shell. Selecciona el ícono cuadrado verde en la parte superior de la pantalla para abrir el **emulador de terminal MATE**, luego escribe algo al azar y presiona enter.

### **Ejemplo de Terminal**

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al seleccionar el ícono, se abrió la aplicación **MATE terminal emulator**, que ha sido preconfigurada para usar un **intérprete de lenguaje de comandos**. En este caso, se nos da una pista del intérprete en uso al ver el símbolo **$**. Este símbolo se usa en **Bash, Ksh, POSIX**, y muchos otros lenguajes de shell para marcar el inicio del **prompt** del shell, donde el usuario puede comenzar a escribir comandos. Cuando escribimos nuestro texto aleatorio y presionamos enter, el intérprete nos informó que no reconoció el comando que ingresamos. Este es **Bash** indicando que no reconoció la instrucción.

Otra forma de identificar el intérprete de lenguaje es viendo los procesos que están ejecutándose en la máquina. En Linux, podemos hacer esto usando el siguiente comando:

### **Validación del Shell con 'ps'**

```bash
sherlock28@htb[/htb]$ ps

    PID TTY          TIME CMD
   4232 pts/1    00:00:00 bash
  11435 pts/1    00:00:00 ps
```

También podemos descubrir qué lenguaje de shell se está utilizando viendo las **variables de entorno** con el comando `env`:

### **Validación del Shell usando 'env'**

```bash
sherlock28@htb[/htb]$ env

SHELL=/bin/bash
```

Ahora, selecciona el ícono cuadrado azul en la parte superior de la pantalla en **Pwnbox**.

### PowerShell vs. Bash

Seleccionar este ícono también abre la aplicación **MATE terminal**, pero esta vez utiliza un **intérprete de lenguaje de comandos** diferente. Al compararlos lado a lado, podemos identificar algunas diferencias clave.

**¿Qué diferencias podemos identificar?**

1. **Sintaxis de comandos**: PowerShell y Bash utilizan una sintaxis diferente para realizar las mismas tareas. Por ejemplo:
   * En Bash, se usa `ls` para listar archivos, mientras que en PowerShell se usa `Get-ChildItem`.
   * En Bash, `cat` muestra el contenido de un archivo, mientras que en PowerShell se usa `Get-Content`.
2. **Manejo de objetos vs. texto**: PowerShell maneja objetos mientras que Bash se enfoca en manejar cadenas de texto. En PowerShell, el resultado de un comando puede ser un objeto completo con propiedades y métodos, mientras que en Bash, es texto plano.
3. **Plataforma**: Bash es el shell predeterminado en la mayoría de los sistemas Linux y MacOS, mientras que PowerShell es nativo de Windows, aunque actualmente es multiplataforma.
4. **Integración con el sistema**: PowerShell tiene una integración más profunda con las APIs y funciones del sistema operativo Windows, mientras que Bash está más orientado a las utilidades del sistema Unix/Linux.

**¿Por qué usaríamos uno sobre el otro en el mismo sistema?**

* **Compatibilidad con comandos específicos**: Si estamos en un sistema Windows y necesitamos interactuar con herramientas nativas de Windows (como servicios, registro o archivos del sistema), PowerShell será más útil debido a su integración profunda. Sin embargo, en sistemas basados en Unix, Bash ofrece una mayor familiaridad con las utilidades del sistema y scripts nativos.
* **Flujo de trabajo preferido**: Algunos administradores de sistemas o desarrolladores prefieren la manipulación de objetos de PowerShell para automatización avanzada, mientras que otros prefieren la simplicidad y flexibilidad del scripting en Bash.

#### Puntos clave

* **Los emuladores de terminal** no están atados a un lenguaje específico, lo que significa que podemos cambiar y personalizar el **lenguaje del shell** para adaptarlo a nuestras necesidades.
* **PowerShell y Bash** tienen diferencias sustanciales en términos de **sintaxis**, **manejo de datos**, y **profundidad de integración** con el sistema operativo.
