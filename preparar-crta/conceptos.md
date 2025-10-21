# Conceptos

## 1.1 What is Red Teaming ?

Un _Red Team_ es un grupo de hackers con diversos perfiles que se encargan de poner a prueba la infraestructura de una organización.

• Los ataques realizados por los _Red Teams_ se dividen en 3 categorías:

* **Cibernéticos** (ataques digitales que incluyen Web, redes y otras tecnologías en la nube)
* **Sociales** (explotación del comportamiento humano)
* **Físicos** (ataques que implican la intervención física directa)

• Las operaciones de un _Red Team_ son similares a una prueba de penetración (_pentest_), pero están más enfocadas y dirigidas.

• Uno de los principales objetivos de un _Red Team_ es evaluar las capacidades de detección y respuesta de la organización.

• Los ataques realizados por los _Red Teams_ son similares a las técnicas empleadas por actores de amenazas con intenciones maliciosas.

• **Emulación de Red Team**: Consiste en copiar técnicas de ataque utilizadas por actores de amenazas (como sustituto).\
• **Simulación de Red Team**: Imita el comportamiento de dichos actores.

• Un _Red Team_ intentará acceder a información sensible por cualquier medio posible, de la forma más sigilosa, procurando no dejar huellas.

• Las simulaciones de ataques en escenarios reales realizadas por un _Red Team_ están orientadas a mejorar significativamente la efectividad de la infraestructura de la organización.

• Grandes empresas como Microsoft y agencias de defensa utilizan _Red Teams_ cibernéticos para llevar a cabo evaluaciones en sus propias redes.

#### **Pruebas de Penetración (Penetration Testing)**

* Es un ataque controlado contra un **host, red o aplicación**, con el objetivo de **medir e identificar riesgos** asociados con la explotación de un entorno específico.
* Se enfoca más en la **reducción de vulnerabilidades expuestas**, configuraciones erróneas, y otros fallos de seguridad conocidos.

#### **Red Teaming**

* Es el proceso de utilizar **tácticas y técnicas reales**, empleadas por actores de amenazas, para **evaluar la efectividad de las personas, tecnologías y procesos** encargados de defender un entorno.
* El enfoque está en la **capacitación del personal** y en **medir tanto los riesgos como las capacidades de defensa** de la organización.

| Característica         | Penetration Testing                                                          | Red Teaming                                                                                      |
| ---------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Alcance**            | Limitado: 1 o 2 sistemas o redes específicos                                 | Puede cubrir una parte o toda la organización                                                    |
| **Supuestos**          | Hace suposiciones sobre el entorno; **no prueba fuera del alcance definido** | **No hace suposiciones**; intenta comprometer cualquier área posible                             |
| **Herramientas**       | Utiliza herramientas disponibles **en el momento de la prueba**              | Investiga y emplea **nuevas vulnerabilidades y herramientas** tan pronto como están disponibles  |
| **Adaptabilidad**      | Menos flexible; pruebas planificadas y estructuradas                         | Altamente adaptable; cambia técnicas y estrategias según el entorno y los resultados             |
| **Objetivo principal** | Identificar y mitigar vulnerabilidades técnicas                              | Evaluar la **resiliencia global** y la **capacidad de detección y respuesta** de la organización |

1.2 Red Team Attack Lifecycle (Phases)
--------------------------------------

#### **1. Reconocimiento Extensivo (Extensive OSINT)**

* En esta fase se recolecta la **mayor cantidad posible de información** sobre la organización objetivo.
* Se da especial atención a redes sociales y plataformas en las que los empleados son activos.
* Los atacantes analizan grandes volúmenes de información pública con el fin de identificar **datos sensibles reutilizables** para explotar más adelante.

#### **2. Acceso Inicial y Ejecución (Initial Access & Execution)**

* **Acceso Inicial:** Se utilizan distintos vectores para obtener acceso a la red interna.
  * Ejemplos: Explotación de servicios remotos externos, fallos de configuración en aplicaciones web, etc.
  * La elección del vector depende de las tecnologías identificadas en la fase OSINT.
* **Ejecución:** Se refiere a la ejecución de código malicioso en el sistema objetivo.
  * Ejemplo: Un atacante puede usar una herramienta de acceso remoto para ejecutar comandos que realicen descubrimiento de red.

#### **3. Persistencia y Escalada de Privilegios (Persistence & Privilege Escalation)**

* **Persistencia:** Los atacantes implementan técnicas para **mantener acceso** incluso tras reinicios o cambios de credenciales.
  * Ejemplo: Restablecer la contraseña de un usuario poco relevante para mantener una puerta trasera.
* **Escalada de Privilegios:** Obtener permisos más altos en el sistema o red.
  * Vías comunes: Explotación de debilidades del sistema, configuraciones erróneas y vulnerabilidades.
  * Niveles de acceso buscados:
    * SYSTEM/root
    * Administrador local
    * Usuarios con privilegios elevados
    * Grupos privilegiados

#### **4. Movimiento Lateral (Lateral Movement)**

* El atacante **se desplaza por la red**, comprometiendo otros dispositivos tras controlar uno inicial.
* Puede usar herramientas propias o credenciales legítimas con utilidades del sistema operativo para moverse sin ser detectado.
* Ejemplos:
  * **Phishing Interno:** Envío de correos dentro de la red para comprometer otras cuentas/sistemas.
  * **Servicios Remotos:** Uso de credenciales válidas para acceder por SSH, VNC, RDP, etc.

#### **5. Evasión de Defensa (Defensive Evasion)**

* Técnicas diseñadas para **evadir la detección** durante el compromiso del sistema.
* Incluye:
  * Ofuscación de scripts maliciosos
  * Ocultamiento en procesos legítimos
  * Desactivación de software de seguridad
* Ejemplo:
  * **Deteriorar Defensas:** Desactivar firewalls, antivirus u otros mecanismos de auditoría.

#### **6. Descubrimiento (Discovery)**

* El atacante busca **comprender el entorno de la organización**, obteniendo conocimiento situacional.
* Permite identificar:
  * Arquitectura de red
  * Activos críticos
  * Usuarios, dispositivos, y más
* Ejemplo:
  * **Descubrimiento de Archivos y Directorios:** Buscar archivos específicos en sistemas o recursos compartidos.

#### **7. Recolección de Datos (Data Collection)**

* Se recopila **información sensible** desde sistemas comprometidos.
* Ejemplos:
  * **Archivado de Datos:** Comprimir o cifrar los datos antes de la exfiltración.
  * **Datos del Portapapeles:** Extraer información copiada entre aplicaciones.

#### **8. Exfiltración de Datos (Data Exfiltration)**

* Una vez recolectada y empaquetada la información, el atacante procede a **extraerla del entorno comprometido**.
* Ejemplos:
  * **Exfiltración Automatizada:** Uso de procesos automáticos para enviar documentos fuera de la red.
  * **Medio Físico:** Transferencia de datos usando dispositivos removibles (USB, discos externos, etc.).



## 1.3 Red Team Infrastructure (Nomenclature)

#### **C2 Server (Servidor de Comando y Control)**

* Es utilizado por los atacantes para **mantener comunicación con los sistemas comprometidos** dentro de la red objetivo.

<figure><img src="../.gitbook/assets/image (1631).png" alt=""><figcaption></figcaption></figure>

#### **Payload Server (Servidor de Cargas Maliciosas)**

* Servidor dedicado a alojar scripts maliciosos, ejecutables, etc.
* **Accesible desde la red del atacante y de la víctima**, actúa como repositorio de herramientas maliciosas.

<figure><img src="../.gitbook/assets/image (1632).png" alt=""><figcaption></figcaption></figure>

#### **Redirector Server (Servidor Redireccionador)**

* Proxy intermedio que **redirecciona todo el tráfico al servidor C2 real**.
* Los atacantes no usan un único sistema para lanzar ataques y recibir shells, sino múltiples sistemas intermedios (pivot points).
* Esto oculta la verdadera ubicación del C2, y permite desplegar y eliminar rápidamente estos servidores para dificultar la detección.

<figure><img src="../.gitbook/assets/image (1633).png" alt=""><figcaption></figcaption></figure>

#### **Adversary Emulation vs Adversary Simulation**

* **Emulación del adversario:** Se imita el comportamiento (TTPs) de un grupo de amenazas específico, como FIN10, basándose en inteligencia de amenazas.
* **Simulación del adversario:** Se simula un ataque real sin haber un actor externo involucrado. Se usan técnicas que funcionan en el entorno, sin importar qué APT las utilice.

#### **APT (Amenaza Persistente Avanzada)**

* Actor de amenazas sigiloso (normalmente un Estado o grupo patrocinado por un Estado) que obtiene acceso no autorizado y **permanece oculto durante largos períodos**.

#### **Exploit y Vulnerabilidad**

* **Vulnerabilidad:** Punto débil en software o hardware que permite comprometer un sistema.
* **Exploit:** Código que **aprovecha una vulnerabilidad** para ganar acceso o ejecutar acciones maliciosas.

#### **DMZ (Zona Desmilitarizada)**

* Segmento de red que expone servicios (como correo, DNS o web) a redes no confiables, manteniendo el resto de la red **protegida del acceso externo**.

#### **MZ (Zona Militarizada)**

* Segmento **más seguro** de la red.
* Alberga **información crítica** y desde donde se gestionan las operaciones de la organización.

#### **TTPs: Tácticas, Técnicas y Procedimientos**

* **Táctica:** Qué intenta lograr el atacante (p. ej. persistencia, evasión).
* **Técnica:** Cómo lo logra (p. ej. uso de malware en procesos legítimos).
* **Procedimiento:** Detalles específicos de implementación.

#### **Listener**

* Componente que **escucha conexiones entrantes** del sistema objetivo.
* Herramientas como **netcat** se utilizan para este propósito en Linux y Windows.
* En el laboratorio, el listener estará activo en Kali y la víctima se conectará tras una explotación exitosa.

#### **Explotación (Exploitation)**

* Ocurre **después de identificar una vulnerabilidad**.
* El atacante ejecuta un exploit para obtener acceso al sistema.
* Si tiene éxito, se ejecuta el **payload**, que puede instalar más herramientas o establecer comunicación con el atacante.

<figure><img src="../.gitbook/assets/image (1634).png" alt=""><figcaption></figcaption></figure>

#### **Proceso de Explotación**

```
Exploit → Si tiene éxito → Payload se ejecuta → Comunicación y acciones maliciosas
```

#### **Tipos de Payloads**

* **Singles:** Autónomos, realizan tareas específicas (crear usuario, bind shell).\
  Ej: `payload/windows/adduser`
* **Stagers:** Cargan **payloads más grandes** desde el atacante.\
  Ej: `payload/windows/shell/bind_tcp`
* **Stages:** Payloads grandes ejecutados por los _stagers_, permiten acciones complejas (ej. meterpreter, escritorio remoto).\
  Ej: `payload/windows/shell/bind_tcp`

#### **Shells**

* Interacción sin GUI con el sistema comprometido (administración remota vía comandos).
* Ejemplos:
  * **Windows:** Command Prompt, PowerShell
  * **Linux:** Bash, sh

**Reverse Shell**

* La **máquina víctima se conecta de vuelta** al equipo atacante.
* Se requiere un listener activo en el equipo del atacante.
* Todo pasa por **puertos TCP específicos**.
* Herramientas como **netcat** son comúnmente utilizadas.

<figure><img src="../.gitbook/assets/image (1635).png" alt=""><figcaption></figcaption></figure>

#### **Bind Shell**

* El **atacante se conecta al puerto TCP** expuesto en la máquina víctima.
* La shell se aloja en la víctima, y quien se conecte al puerto obtendrá acceso.
* Permite al atacante **expandir el compromiso** dentro del sistema.



<figure><img src="../.gitbook/assets/image (1636).png" alt=""><figcaption></figcaption></figure>

## 1.4 Enterprise Environment Overview

#### **Web Server (Servidor Web)**

* Es un software que interpreta **URLs** y el protocolo **HTTP**, utilizado por los navegadores para acceder a páginas web.
* También puede entenderse como un computador donde se almacena el contenido web, es decir, **donde se alojan los sitios web**.
* Los **servidores web externos** se ubican en la **DMZ (zona desmilitarizada)** y atienden las solicitudes de los clientes.
* Suelen estar **conectados a la red interna empresarial** para acceder a bases de datos u otros servicios.

#### **Mail Server (Servidor de Correo Electrónico)**

* Se encarga de **gestionar y entregar correos electrónicos** a través de una red, normalmente Internet.
* Puede **recibir correos** desde clientes y **reenviarlos a otros servidores o usuarios**.
* Cuando presionas “Enviar” en tu cliente de correo, este se conecta a un servidor SMTP (_Simple Mail Transfer Protocol_), que **gestiona el envío de correos** entre clientes y servidores.
* Para **recibir correos**, el cliente se conecta a un **servidor POP3** (o IMAP en implementaciones más modernas).

#### **Database Server (Servidor de Base de Datos / SQL Server)**

* Se utiliza para **almacenar, gestionar y permitir el acceso centralizado a bases de datos**.
* Es esencial en organizaciones con **grandes volúmenes de datos**.
* Los usuarios y aplicaciones pueden interactuar con él para **consultar, modificar, añadir o eliminar datos** mediante **consultas SQL (Structured Query Language)**.

#### **Bastion Host (Servidor Baluarte / Jump Server)**

* Es un equipo de propósito específico, **diseñado y configurado para resistir ataques**.
* Suele ejecutar una sola aplicación crítica (como un **servidor proxy**) y se **desactivan o restringen todos los servicios innecesarios** para minimizar la superficie de ataque.
* Se usa típicamente como **puerta de entrada controlada a redes internas críticas**, común en infraestructuras con segmentación de red.

#### **Automation Server (Servidor de Automatización)**

* Fundamental en los flujos de trabajo de desarrollo de software.
* Automatiza tareas como **compilación, pruebas y despliegue**, facilitando la **Integración y Entrega Continua (CI/CD)**.
* Ejemplos comunes:
  * **Jenkins**
  * **TeamCity**
  * **Bamboo**

#### **Active Directory (Directorio Activo)**

* Es una **base de datos o directorio** que:
  * **Gestiona los recursos de la organización** (usuarios, computadoras, carpetas compartidas, etc.)
  * **Define reglas de acceso y relaciones** entre dichos recursos.
  * **Centraliza la administración** de los activos virtuales de la organización.
  * Hace que la información de objetos de red esté **disponible para usuarios y administradores**.

<figure><img src="../.gitbook/assets/image (1637).png" alt=""><figcaption></figcaption></figure>

### **Bosques y Dominios en Active Directory**

* Un **bosque (forest)** es una **instancia única de Active Directory**.
* Es, esencialmente, una **colección de controladores de dominio (Domain Controllers)** que confían entre sí.
* Los **dominios** son contenedores lógicos **dentro del bosque**.
* Dentro de cada dominio existen:
  * **Unidades Organizativas (OUs):** Agrupaciones lógicas de usuarios, equipos y otros recursos.
  * **Grupos:**
    * Colecciones de usuarios u otros grupos.
    * Pueden ser **privilegiados** (con permisos elevados) o **no privilegiados**.

### **Objetos de Active Directory**

Los **objetos** son las entidades físicas o lógicas que conforman una red organizada.

#### Usuarios de Dominio (Domain Users)

* Cuentas de usuario autorizadas para **autenticarse** en computadoras o servidores del dominio.

#### Grupos de Dominio (Domain Groups / Grupos Globales)

* Se utilizan para **asignar permisos** a recursos ubicados en **cualquier dominio del bosque**.

#### Equipos de Dominio (Domain Computers)

* Computadoras que están **unidas al dominio** y se convierten en **miembros** del mismo.

### **Controlador de Dominio (Domain Controller)**

* Servidor central que:
  * **Responde a solicitudes de autenticación de seguridad**.
  * **Administra recursos** como usuarios, grupos, políticas y equipos.

### **GPOs – Objetos de Directiva de Grupo (Group Policy Objects)**

* Conjunto de políticas que se **aplican a usuarios, objetos de dominio, computadoras**, etc.
* Se utilizan para controlar aspectos de configuración del entorno como:
  * Contraseñas
  * Escritorio del usuario
  * Accesos a sistemas

### Tickets de Autenticación en Active Directory (Kerberos)

#### **TGT (Ticket Granting Ticket)**

* **Boleto utilizado para la autenticación** de usuarios dentro del dominio.
* Emitido por el **KDC (Key Distribution Center)** una vez que el usuario inicia sesión.

#### **TGS (Ticket Granting Service)**

* **Boleto utilizado para la autorización**, es decir, para obtener acceso a servicios específicos dentro de la red una vez autenticado.
