# Sessions

**MSFconsole** puede gestionar múltiples módulos al mismo tiempo. Esta es una de las muchas razones por las que proporciona al usuario tanta flexibilidad. Esto se logra mediante el uso de **Sesiones**, que crean interfaces de control dedicadas para todos los módulos desplegados.

Una vez que se crean varias sesiones, podemos alternar entre ellas y vincular un módulo diferente a una de las sesiones en segundo plano para ejecutarlo o convertirlas en trabajos. Ten en cuenta que una vez que una sesión se coloca en segundo plano, seguirá ejecutándose y nuestra conexión con el host objetivo persistirá. Sin embargo, las sesiones pueden morir si algo sale mal durante el tiempo de ejecución del payload, lo que provoca que el canal de comunicación se cierre.

## Uso de Sesiones

Mientras ejecutamos cualquier exploit o módulo auxiliar disponible en `msfconsole`, podemos enviar la sesión a segundo plano siempre que se forme un canal de comunicación con el host objetivo. Esto se puede hacer presionando la combinación de teclas `[CTRL] + [Z]` o escribiendo el comando `background` en el caso de las etapas de Meterpreter. Esto nos mostrará un mensaje de confirmación. Después de aceptar el aviso, volveremos al símbolo de `msfconsole` (msf6 >) y podremos lanzar inmediatamente un módulo diferente.

### Listando Sesiones Activas

Podemos usar el comando `sessions` para ver nuestras sesiones activas actuales.

```bash
msf6 exploit(windows/smb/psexec_psh) > sessions
```

```markdown
Active sessions
===============

  Id  Name  Type                     Information                 Connection
  --  ----  ----                     -----------                 ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501 (10.10.10.205)
```

### Interactuando con una Sesión

Puedes usar el comando `sessions -i [número]` para abrir una sesión específica.

```bash
msf6 exploit(windows/smb/psexec_psh) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > 
```

Esto es especialmente útil cuando queremos ejecutar un módulo adicional en un sistema ya explotado con un canal de comunicación formado y estable.

Esto se puede hacer enviando a segundo plano nuestra sesión actual, que se forma debido al éxito del primer exploit, buscando el segundo módulo que deseamos ejecutar y, si lo permite el tipo de módulo seleccionado, seleccionando el número de sesión en la que se debe ejecutar el módulo. Esto se puede hacer desde el menú `show options` del segundo módulo.

Generalmente, estos módulos se pueden encontrar en la categoría **post**, que se refiere a los módulos de post-explotación. Los principales arquetipos de módulos en esta categoría consisten en recolectores de credenciales, sugeridores de exploits locales y escáneres de red interna.

## Trabajos

Si, por ejemplo, estamos ejecutando un exploit activo bajo un puerto específico y necesitamos este puerto para un módulo diferente, no podemos simplemente terminar la sesión usando `[CTRL] + [C]`. Si lo hiciéramos, veríamos que el puerto seguiría en uso, afectando nuestra capacidad de usar el nuevo módulo. Por lo tanto, necesitaríamos usar el comando `jobs` para ver las tareas actualmente activas que se están ejecutando en segundo plano y terminar las antiguas para liberar el puerto.

Otros tipos de tareas dentro de las sesiones también pueden convertirse en trabajos para ejecutarse en segundo plano sin problemas, incluso si la sesión muere o desaparece.

### Visualizando el Menú de Ayuda del Comando Jobs

Podemos ver el menú de ayuda para este comando, al igual que para otros, escribiendo `jobs -h`.

```bash
msf6 exploit(multi/handler) > jobs -h
```

Manipulación e interacción de trabajos activos.

**OPCIONES:**

* `-K` Termina todos los trabajos en ejecución.
* `-P` Persistir todos los trabajos en ejecución al reiniciar.
* `-S <opt>` Filtro de búsqueda de fila.
* `-h` Banner de ayuda.
* `-i <opt>` Lista información detallada sobre un trabajo en ejecución.
* `-k <opt>` Termina trabajos por ID de trabajo y/o rango.
* `-l` Lista todos los trabajos en ejecución.
* `-p <opt>` Agrega persistencia al trabajo por ID de trabajo.
* `-v` Imprime información más detallada. Usa con `-i` y `-l`.

### Visualizando el Menú de Ayuda del Comando Exploit

Cuando ejecutamos un exploit, podemos ejecutarlo como un trabajo escribiendo `exploit -j`. Según el menú de ayuda para el comando `exploit`, agregar `-j` a nuestro comando en lugar de solo `exploit` o `run` lo "ejecutará en el contexto de un trabajo."

```bash
msf6 exploit(multi/handler) > exploit -h
```

Inicia un intento de explotación.

**OPCIONES:**

* `-J` Forzar la ejecución en primer plano, incluso si es pasivo.
* `-e <opt>` El codificador de payload a utilizar. Si no se especifica, se utiliza ENCODER.
* `-f` Forzar que el exploit se ejecute independientemente del valor de MinimumRank.
* `-h` Banner de ayuda.
* `-j` Ejecutar en el contexto de un trabajo.

### Ejecutando un Exploit como un Trabajo en Segundo Plano

```bash
msf6 exploit(multi/handler) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
[*] Started reverse TCP handler on 10.10.14.34:4444
```

#### Listando Trabajos en Ejecución

Para listar todos los trabajos en ejecución, podemos usar el comando `jobs -l`. Para matar un trabajo específico, observa el número de índice del trabajo y usa el comando `kill [número de índice]`. Usa el comando `jobs -K` para matar todos los trabajos en ejecución.

```bash
msf6 exploit(multi/handler) > jobs -l
```

**Trabajos**

```bash
Id  Name                    Payload                    Payload opts
--  ----                    -------                    ------------
0   Exploit: multi/handler  generic/shell_reverse_tcp  tcp://10.10.14.34:4444
```

A continuación, trabajaremos con el extremadamente poderoso payload de Meterpreter.
