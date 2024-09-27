# Spawning Interactive Shells

Al final de la última sección, establecimos una sesión de shell con el objetivo. Inicialmente, nuestro shell era limitado (a veces se le llama shell de cárcel), así que utilizamos Python para generar un shell TTY Bourne para darnos acceso a más comandos y un prompt desde el cual trabajar. Probablemente, esta será una situación en la que nos encontraremos cada vez más a medida que practiquemos en Hack The Box y en el mundo real en compromisos.

Puede haber ocasiones en que aterrizamos en un sistema con un shell limitado, y Python no está instalado. En estos casos, es bueno saber que podemos usar varios métodos diferentes para generar un shell interactivo. Examinemos algunos de ellos.

Ten en cuenta que siempre que veamos `/bin/sh` o `/bin/bash`, esto también podría reemplazarse con el binario asociado con el lenguaje de intérprete de shell presente en ese sistema. En la mayoría de los sistemas Linux, es probable que nos encontremos con el shell Bourne (`/bin/sh`) y el shell Bourne Again (`/bin/bash`) presentes de manera nativa en el sistema.

## /bin/sh -i

Este comando ejecutará el intérprete de shell especificado en la ruta en modo interactivo (-i).

```bash
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

## Perl

Si el lenguaje de programación Perl está presente en el sistema, estos comandos ejecutarán el intérprete de shell especificado.

```bash
perl -e 'exec "/bin/sh";'
```

## Ruby

Si el lenguaje de programación Ruby está presente en el sistema, este comando ejecutará el intérprete de shell especificado:

```bash
ruby -e 'exec "/bin/sh"'
```

## Lua

Si el lenguaje de programación Lua está presente en el sistema, podemos usar el método `os.execute` para ejecutar el intérprete de shell especificado utilizando el siguiente comando completo:

```bash
lua -e "os.execute('/bin/sh')"
```

## AWK

AWK es un lenguaje de procesamiento y escaneo de patrones similar a C, presente en la mayoría de los sistemas UNIX/Linux, ampliamente utilizado por desarrolladores y administradores de sistemas para generar informes. También se puede usar para generar un shell interactivo. Esto se muestra en el siguiente script corto de AWK:

```bash
awk 'BEGIN {system("/bin/sh")}'
```

## Find

`find` es un comando presente en la mayoría de los sistemas Unix/Linux, ampliamente utilizado para buscar archivos y directorios utilizando varios criterios. También se puede usar para ejecutar aplicaciones e invocar un intérprete de shell.

```bash
find / -name nombredefichero -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```

Este uso del comando `find` está buscando cualquier archivo listado después de la opción `-name`, luego ejecuta `awk (/bin/awk)` y corre el mismo script que discutimos en la sección de AWK para ejecutar un intérprete de shell.

## Usando Exec para Lanzar un Shell

```bash
find . -exec /bin/sh \; -quit
```

Este uso del comando `find` utiliza la opción de ejecución (-exec) para iniciar directamente el intérprete de shell. Si `find` no puede encontrar el archivo especificado, no se obtendrá ningún shell.

### VIM

Sí, podemos establecer el lenguaje del intérprete de shell desde dentro del popular editor de texto basado en línea de comandos VIM. Esta es una situación muy específica en la que nos encontraríamos necesitar usar este método, pero es bueno saberlo por si acaso.

```bash
vim -c ':!/bin/sh'
```

### **Vim Escape**

```bash
vim
:set shell=/bin/sh
:shell
```

## Consideraciones sobre Permisos de Ejecución

Además de conocer todas las opciones listadas anteriormente, debemos tener en cuenta los permisos que tenemos con la cuenta de sesión de shell. Siempre podemos intentar ejecutar este comando para listar las propiedades de los archivos y los permisos que tiene nuestra cuenta sobre cualquier archivo o binario dado:

```bash
ls -la <ruta/al/archivoobinary>
```

También podemos intentar ejecutar este comando para verificar qué permisos de `sudo` tiene la cuenta en la que aterrizamos:

```bash
sudo -l
```

```ruby
Matching Defaults entries for apache on ILF-WebSrv:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL
```

El comando `sudo -l` anterior necesitará un shell interactivo estable para ejecutarse. Si no estás en un shell completo o sentado en un shell inestable, es posible que no obtengas ninguna respuesta de él. No solo considerar los permisos nos permitirá ver qué comandos podemos ejecutar, sino que también puede comenzar a darnos una idea de los posibles vectores que nos permitirán escalar privilegios.

