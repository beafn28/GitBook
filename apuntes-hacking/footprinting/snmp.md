# SNMP

El **Protocolo simple de administración de redes (SNMP)** fue creado para monitorear dispositivos de red. Además, este protocolo también se puede utilizar para manejar tareas de configuración y cambiar ajustes de forma remota. El hardware habilitado para SNMP incluye enrutadores, conmutadores, servidores, dispositivos IoT y muchos otros dispositivos que también pueden ser consultados y controlados mediante este protocolo estándar. Así, SNMP es un protocolo para el monitoreo y la administración de dispositivos de red. Además, se pueden manejar tareas de configuración y realizar ajustes de forma remota utilizando este estándar. La versión actual es **SNMPv3**, que aumenta la seguridad de SNMP en particular, pero también la complejidad de su uso.

Además del intercambio puro de información, SNMP también transmite comandos de control utilizando agentes a través del **puerto UDP 161**. El cliente puede establecer valores específicos en el dispositivo y cambiar opciones y configuraciones con estos comandos. Mientras que en la comunicación clásica siempre es el cliente quien solicita activamente la información al servidor, **SNMP también permite el uso de los llamados traps** a través del **puerto UDP 162**. Estos son paquetes de datos enviados desde el servidor SNMP al cliente sin ser solicitados explícitamente. Si un dispositivo está configurado de manera correspondiente, se envía un trap SNMP al cliente una vez que ocurre un evento específico en el lado del servidor.

Para que el cliente y el servidor SNMP puedan intercambiar los valores respectivos, los objetos SNMP disponibles deben tener direcciones únicas conocidas por ambas partes. Este mecanismo de direccionamiento es un requisito absoluto para transmitir datos y monitorear redes con éxito utilizando SNMP.

### MIB (Base de Información de Gestión)

Para garantizar que el acceso SNMP funcione a través de diferentes fabricantes y con varias combinaciones cliente-servidor, se creó la **Base de Información de Gestión (MIB)**. MIB es un formato independiente para almacenar información de dispositivos. Un MIB es un archivo de texto en el que se listan todos los objetos SNMP consultables de un dispositivo en una jerarquía de árbol estandarizada. Contiene al menos un **Identificador de Objeto (OID)**, que, además de la dirección única necesaria y un nombre, también proporciona información sobre el tipo, los derechos de acceso y una descripción del objeto respectivo. Los archivos MIB están escritos en el formato de texto ASCII basado en la **Notación de Sintaxis Abstracta Uno (ASN.1)**. Los MIBs no contienen datos, pero explican dónde encontrar cierta información y cómo se ve, qué valores se devuelven para el OID específico o qué tipo de datos se utiliza.

### OID (Identificador de Objeto)

Un **OID** representa un nodo en un espacio de nombres jerárquico. Una secuencia de números identifica de forma única cada nodo, lo que permite determinar su posición en el árbol. Cuanto más larga sea la cadena, más específica es la información. Muchos nodos en el árbol OID no contienen nada excepto referencias a los nodos que están por debajo de ellos. Los OIDs consisten en enteros y generalmente se concatenan mediante notación de puntos. Podemos buscar muchos MIBs asociados a OIDs en el **Registro de Identificadores de Objeto**.

### SNMPv1

La **versión 1 del SNMP (SNMPv1)** se utiliza para la gestión y monitoreo de redes. SNMPv1 es la primera versión del protocolo y todavía se usa en muchas redes pequeñas. Soporta la recuperación de información de dispositivos de red, permite la configuración de dispositivos y proporciona traps, que son notificaciones de eventos. Sin embargo, SNMPv1 no tiene un mecanismo de autenticación incorporado, lo que significa que cualquier persona que acceda a la red puede leer y modificar los datos de la red. Otro defecto principal de SNMPv1 es que no soporta encriptación, lo que significa que todos los datos se envían en texto plano y pueden ser interceptados fácilmente.

### SNMPv2

**SNMPv2** existió en diferentes versiones. La versión que todavía existe hoy es **v2c**, y la "c" significa **SNMP basado en comunidad**. En cuanto a la seguridad, SNMPv2 está a la par con SNMPv1 y ha sido ampliado con funciones adicionales del SNMP basado en partes, que ya no se utiliza. Sin embargo, un problema importante con la implementación inicial del protocolo SNMP es que la cadena de comunidad que proporciona seguridad se transmite solo en texto plano, lo que significa que no tiene encriptación incorporada.

### SNMPv3

La seguridad ha aumentado enormemente en **SNMPv3** gracias a funciones de seguridad como la autenticación mediante nombre de usuario y contraseña, y la encriptación de la transmisión (mediante clave precompartida) de los datos. Sin embargo, la complejidad también aumenta en la misma medida, con muchas más opciones de configuración que en **v2c**.

### Cadenas de Comunidad

Las **cadenas de comunidad** pueden verse como contraseñas que se utilizan para determinar si la información solicitada puede ser vista o no. Es importante señalar que muchas organizaciones aún están utilizando **SNMPv2**, ya que la transición a **SNMPv3** puede ser muy compleja, pero los servicios aún necesitan mantenerse activos. Esto genera mucha preocupación a los administradores y crea algunos problemas que desean evitar. La falta de conocimiento sobre cómo se puede obtener la información y cómo los atacantes la utilizan hace que el enfoque de los administradores parezca inexplicable. Al mismo tiempo, la falta de encriptación de los datos enviados también es un problema, ya que cada vez que las cadenas de comunidad se envían a través de la red, pueden ser interceptadas y leídas.

## Configuración Predeterminada

La **configuración predeterminada** del **demonio SNMP** define la configuración básica para el servicio, que incluye las direcciones IP, puertos, MIB, OIDs, autenticación y cadenas de comunidad.

### Configuración del Demonio SNMP

```bash
sherlock28@htb[/htb]$ cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'

sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>
sysServices    72
master  agentx
agentaddress  127.0.0.1,[::1]
view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1
rocommunity  public default -V systemonly
rocommunity6 public default -V systemonly
rouser authPrivUser authpriv -V systemonly
```

La configuración de este servicio se puede modificar de muchas maneras. Por lo tanto, se recomienda configurar una máquina virtual para instalar y configurar el servidor SNMP nosotros mismos. Todas las configuraciones que se pueden realizar para el demonio SNMP están definidas y descritas en la **página del manual (manpage)**.

## Configuraciones Peligrosas

Algunas configuraciones peligrosas que el administrador puede hacer con SNMP son:

| **Configuración**                        | **Descripción**                                                                                 |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `rwuser noauth`                          | Proporciona acceso completo al árbol de OID sin autenticación.                                  |
| `rwcommunity <cadena comunidad> <IPv4>`  | Proporciona acceso completo al árbol de OID sin importar desde dónde se envíen las solicitudes. |
| `rwcommunity6 <cadena comunidad> <IPv6>` | El mismo acceso que con `rwcommunity`, pero usando IPv6.                                        |

## Footprinting del Servicio

Para realizar **footprinting** en SNMP, podemos usar herramientas como `snmpwalk`, `onesixtyone` y `braa`. `Snmpwalk` se utiliza para consultar los **OID** con su información. `Onesixtyone` puede ser utilizado para atacar por fuerza bruta los nombres de las **cadenas de comunidad**, ya que pueden ser nombradas de manera arbitraria por el administrador. Dado que estas cadenas pueden estar vinculadas a cualquier origen, identificar las existentes puede tomar bastante tiempo.

### SNMPwalk

```bash
sherlock28@htb[/htb]$ snmpwalk -v2c -c public 10.129.14.128

iso.3.6.1.2.1.1.1.0 = STRING: "Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (5134) 0:00:51.34
iso.3.6.1.2.1.1.4.0 = STRING: "mrb3n@inlanefreight.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "htb"
iso.3.6.1.2.1.1.6.0 = STRING: "Sitting on the Dock of the Bay"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (0) 0:00:00.00

...SNIP...
```

En caso de una mala configuración, obtendríamos resultados similares a los mostrados anteriormente. Una vez que conocemos la **cadena de comunidad** y el servicio SNMP que no requiere autenticación (versiones 1 y 2c), podemos consultar información del sistema interno como en el ejemplo anterior.

Aquí podemos reconocer algunos paquetes de Python que han sido instalados en el sistema. Si no conocemos la cadena de comunidad, podemos usar **onesixtyone** y las listas de palabras de **SecLists** para identificar estas cadenas de comunidad.

### OneSixtyOne

```bash
sherlock28@htb[/htb]$ sudo apt install onesixtyone
sherlock28@htb[/htb]$ onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt 10.129.14.128

Scanning 1 hosts, 3220 communities
10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
```

A menudo, cuando ciertas **cadenas de comunidad** están vinculadas a direcciones IP específicas, se nombran con el nombre del **hostname** del host, y a veces incluso se agregan símbolos a estos nombres para hacerlos más difíciles de identificar. Sin embargo, si imaginamos una red extensa con más de 100 servidores diferentes gestionados con SNMP, las etiquetas tendrán algún patrón. Por lo tanto, podemos usar diferentes reglas para adivinarlas. Podemos utilizar la herramienta **crunch** para crear listas de palabras personalizadas. Crear listas de palabras personalizadas no es una parte esencial de este módulo, pero se pueden encontrar más detalles en el módulo **Cracking Passwords With Hashcat**.

Una vez que conocemos una **cadena de comunidad**, podemos usarla con **braa** para realizar fuerza bruta sobre los OID individuales y enumerar la información que hay detrás de ellos.

### Braa

```bash
sherlock28@htb[/htb]$ sudo apt install braa
sherlock28@htb[/htb]$ braa <community string>@<IP>:.1.3.6.*   # Sintaxis
sherlock28@htb[/htb]$ braa public@10.129.14.128:.1.3.6.*
```

Resultado:

```bash
10.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
10.129.14.128:20ms:.1.3.6.1.2.1.1.4.0:mrb3n@inlanefreight.htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.5.0:htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.6.0:US
10.129.14.128:20ms:.1.3.6.1.2.1.1.7.0:78
...SNIP...
```

Una vez más, es importante señalar que la configuración independiente del servicio **SNMP** traerá una gran variedad de experiencias diferentes que ningún tutorial puede reemplazar. Por lo tanto, recomendamos encarecidamente configurar una máquina virtual con SNMP, experimentar con ella y probar diferentes configuraciones. **SNMP** puede ser una ventaja para un administrador de sistemas, pero también puede ser una pesadilla para los analistas y gerentes de seguridad.
