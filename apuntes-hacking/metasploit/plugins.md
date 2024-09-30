# Plugins

Los **plugins** son software disponibles que ya han sido lanzados por terceros y han recibido la aprobación de los creadores de Metasploit para integrarse en el marco. Estos pueden representar productos comerciales que tienen una edición comunitaria para uso gratuito, pero con funcionalidades limitadas, o pueden ser proyectos individuales desarrollados por personas individuales.

El uso de plugins facilita aún más la vida de un pentester, trayendo la funcionalidad de software bien conocido al entorno de `msfconsole` o Metasploit Pro. Anteriormente, teníamos que alternar entre diferentes software para importar y exportar resultados, configurando opciones y parámetros una y otra vez. Ahora, con el uso de plugins, todo se documenta automáticamente en la base de datos que estamos utilizando, y los hosts, servicios y vulnerabilidades están disponibles a simple vista para el usuario. Los plugins trabajan directamente con la API y pueden ser utilizados para manipular todo el marco. Pueden ser útiles para automatizar tareas repetitivas, agregar nuevos comandos a `msfconsole` y extender el ya poderoso marco.

## Usando Plugins

Para comenzar a usar un plugin, debemos asegurarnos de que esté instalado en el directorio correcto en nuestra máquina. Navegando a `/usr/share/metasploit-framework/plugins`, que es el directorio predeterminado para cada nueva instalación de `msfconsole`, deberíamos ver qué plugins tenemos disponibles:

```bash
sherlock28@htb[/htb]$ ls /usr/share/metasploit-framework/plugins
```

Salida de ejemplo:

```arduino
aggregator.rb      beholder.rb        event_tester.rb  komand.rb
msfd.rb            nexpose.rb        request.rb       session_notifier.rb
sounds.rb         token_adduser.rb  wmap.rb
alias.rb          db_credcollect.rb  ffautoregen.rb   lab.rb
msgrpc.rb         openvas.rb       rssfeed.rb       session_tagger.rb
sqlmap.rb         token_hunter.rb
auto_add_route.rb  db_tracker.rb     ips_filter.rb    libnotify.rb
nessus.rb         pcap_log.rb      sample.rb        socket_logger.rb
thread.rb         wiki.rb
```

Si el plugin se encuentra aquí, podemos activarlo dentro de `msfconsole` y veremos la salida de saludo para ese plugin específico, indicando que se ha cargado correctamente y ahora está listo para usar:

### Cargando el Plugin Nessus

```plaintext
msf6 > load nessus

[*] Nessus Bridge for Metasploit
[*] Type nessus_help for a command listing
[*] Successfully loaded Plugin: Nessus
```

Ahora, podemos ver la ayuda del plugin para conocer los comandos disponibles:

```plaintext
msf6 > nessus_help

Command                     Help Text
-------                     ---------
Generic Commands            
-----------------           -----------------
nessus_connect              Connect to a Nessus server
nessus_logout               Logout from the Nessus server
nessus_login                Login into the connected Nessus server with a different username
<SNIP>
nessus_user_del             Delete a Nessus User
nessus_user_passwd          Change Nessus Users Password
                            
Policy Commands             
-----------------           -----------------
nessus_policy_list          List all policies
nessus_policy_del           Delete a policy
```

Si el plugin no está instalado correctamente, recibiremos el siguiente error al intentar cargarlo:

```plaintext
msf6 > load Plugin_That_Does_Not_Exist

[-] Failed to load plugin from /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb: cannot load such file -- /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb
```

Para comenzar a utilizar el plugin, comienza a emitir los comandos disponibles en el menú de ayuda de ese plugin específico. Cada integración multiplataforma ofrece un conjunto único de interacciones que podemos usar durante nuestras evaluaciones, por lo que es útil informarse sobre cada uno de ellos antes de emplearlos para aprovechar al máximo tenerlos a nuestra disposición.

## Instalación de Nuevos Plugins

Los **plugins** nuevos y más populares se instalan con cada actualización de la distribución de Parrot OS a medida que son lanzados al público por sus creadores, recopilados en el repositorio de actualizaciones de Parrot. Para instalar nuevos plugins personalizados que no están incluidos en las actualizaciones de la distribución, podemos tomar el archivo .rb proporcionado en la página del creador y colocarlo en la carpeta en `/usr/share/metasploit-framework/plugins` con los permisos adecuados.

Por ejemplo, intentemos instalar los Plugins de Metasploit de DarkOperator. Luego, siguiendo el enlace anterior, obtendremos un par de archivos Ruby (.rb) que podemos colocar directamente en la carpeta mencionada.

### Descargando Plugins de MSF

```bash
sherlock28@htb[/htb]$ git clone https://github.com/darkoperator/Metasploit-Plugins
sherlock28@htb[/htb]$ ls Metasploit-Plugins
```

Salida de ejemplo:

```arduino
aggregator.rb      ips_filter.rb  pcap_log.rb          sqlmap.rb
alias.rb           komand.rb      pentest.rb           thread.rb
auto_add_route.rb  lab.rb         request.rb           token_adduser.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_hunter.rb
db_credcollect.rb  msfd.rb        sample.rb            twitt.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
growl.rb           openvas.rb     sounds.rb
```

Aquí podemos tomar como ejemplo el plugin `pentest.rb` y copiarlo a `/usr/share/metasploit-framework/plugins`.

### Copiando el Plugin a MSF

```bash
sherlock28@htb[/htb]$ sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb
```

Después, lanzamos `msfconsole` y comprobamos la instalación del plugin ejecutando el comando `load`. Una vez que el plugin ha sido cargado, el menú de ayuda en `msfconsole` se extiende automáticamente con funciones adicionales.

### Cargando el Plugin

```bash
sherlock28@htb[/htb]$ msfconsole -q

msf6 > load pentest

       ___         _          _     ___ _           _
      | _ \___ _ _| |_ ___ __| |_  | _ \ |_  _ __ _(_)_ _
      |  _/ -_) ' \  _/ -_|_-<  _| |  _/ | || / _` | | ' \
      |_| \___|_||_\__\___/__/\__| |_| |_|\_,_\__, |_|_||_|
                                              |___/
      
Version 1.6
Pentest Plugin loaded.
by Carlos Perez (carlos_perez[at]darkoperator.com)
[*] Successfully loaded plugin: pentest
```

```plaintext
plaintextCopiar códigomsf6 > help
```

```markdown
Tradecraft Commands
===================

    Command          Description
    -------          ----------- 
    check_footprint  Checks the possible footprint of a post module on a target system.
```

```sql
auto_exploit Commands
=====================

    Command           Description
    -------           ----------- 
    show_client_side  Show matched client side exploits from data imported from vuln scanners.
    vuln_exploit      Runs exploits based on data imported from vuln scanners.
```

```markdown
Discovery Commands
==================

    Command                 Description
    -------                 ----------- 
    discover_db             Run discovery modules against current hosts in the database.
    network_discover        Performs a port-scan and enumeration of services found for non pivot networks.
    pivot_network_discover  Performs enumeration of networks available to a specified Meterpreter session.
    show_session_networks   Enumerate the networks one could pivot thru Meterpreter in the active sessions.
```

```markdown
Project Commands
================

    Command       Description
    -------       ----------- 
    project       Command for managing projects.
```

```mathematica
Postauto Commands
=================

    Command             Description
    -------             ----------- 
    app_creds           Run application password collection modules against specified sessions.
    get_lhost           List local IP addresses that can be used for LHOST.
    multi_cmd           Run shell command against several sessions
    multi_meter_cmd     Run a Meterpreter Console Command against specified sessions.
    multi_meter_cmd_rc  Run resource file with Meterpreter Console Commands against specified sessions.
    multi_post          Run a post module against specified sessions.
    multi_post_rc       Run resource file with post modules and options against specified sessions.
    sys_creds           Run system password collection modules against specified sessions.
```

Muchas personas escriben diferentes plugins para el marco de Metasploit. Todos tienen un propósito específico y pueden ser de gran ayuda para ahorrar tiempo una vez que nos familiaricemos con ellos. Consulta la lista de plugins populares a continuación:

* nMap (preinstalado)
* NexPose (preinstalado)
* Nessus (preinstalado)
* Mimikatz (preinstalado V.1)
* Stdapi (preinstalado)
* Railgun
* Priv
* Incognito (preinstalado)
* Plugins de Darkoperator

## Mixins

El **Marco Metasploit** está escrito en Ruby, un lenguaje de programación orientado a objetos. Esto juega un gran papel en lo que hace que `msfconsole` sea excelente de usar. Los **mixins** son una de esas características que, cuando se implementan, ofrecen una gran flexibilidad tanto al creador del script como al usuario.

Los **mixins** son clases que actúan como métodos para ser usados por otras clases sin necesidad de ser la clase padre de esas otras clases. Por lo tanto, se consideraría inapropiado llamarlo herencia, sino más bien inclusión. Se utilizan principalmente cuando:

* Queremos proporcionar muchas funciones opcionales para una clase.
* Queremos usar una característica particular para una multitud de clases.

La mayor parte del lenguaje de programación Ruby gira en torno a los mixins como módulos. El concepto de mixins se implementa usando la palabra `include`, a la cual pasamos el nombre del módulo como parámetro. Podemos leer más sobre mixins aquí.

Si estamos comenzando con Metasploit, no deberíamos preocuparnos por el uso de mixins o su impacto en nuestra evaluación. Sin embargo, se mencionan aquí como una nota sobre cuán compleja puede llegar a ser la personalización de Metasploit.
