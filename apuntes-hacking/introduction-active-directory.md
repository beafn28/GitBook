# Introduction Active Directory

{% file src="../.gitbook/assets/Introduction_To_Active_Directory_Module_Cheat_Sheet.pdf" %}

<figure><img src="../.gitbook/assets/whyad5.png" alt=""><figcaption></figcaption></figure>

## Estructura de Active Directory

Active Directory (AD) es un servicio de directorio para entornos de red Windows. Es una estructura distribuida y jerárquica que permite la administración centralizada de los recursos de una organización, incluidos usuarios, computadoras, grupos, dispositivos de red y recursos compartidos, directivas de grupo, servidores y estaciones de trabajo, y relaciones de confianza (trusts).

AD proporciona funciones de **autenticación y autorización** dentro de un entorno de dominio Windows. Un servicio de directorio, como **Active Directory Domain Services (AD DS)**, ofrece a una organización formas de almacenar datos de directorio y hacerlos accesibles tanto a usuarios estándar como a administradores en la misma red. AD DS almacena información como nombres de usuario y contraseñas, y gestiona los permisos necesarios para que los usuarios autorizados accedan a esta información.

Se lanzó por primera vez con Windows Server 2000; en los últimos años ha sido objeto de ataques cada vez más sofisticados. Está diseñado para ser compatible hacia atrás, y muchas de sus funciones no son **"seguras por defecto"**. Su correcta administración puede ser difícil, especialmente en entornos grandes donde es fácil cometer errores de configuración.

Las vulnerabilidades y configuraciones incorrectas en Active Directory suelen aprovecharse para conseguir un **punto de apoyo** (acceso interno), moverse lateral o verticalmente dentro de la red y obtener acceso no autorizado a recursos protegidos como bases de datos, recursos compartidos, código fuente y más.

En esencia, AD es una gran base de datos accesible para todos los usuarios del dominio, independientemente de su nivel de privilegio. Una cuenta básica de usuario de AD sin privilegios adicionales puede usarse para enumerar la mayoría de los objetos contenidos en AD, entre ellos:

* Computadoras del dominio
* Usuarios del dominio
* Información de grupos del dominio
* Unidades organizativas (OUs)
* Directiva predeterminada del dominio
* Niveles funcionales del dominio
* Política de contraseñas
* Objetos de directiva de grupo (GPOs)
* Relaciones de confianza del dominio
* Listas de control de acceso (ACLs)

Por esta razón, debemos entender cómo está configurado Active Directory y los fundamentos de su administración antes de intentar atacarlo. Siempre es más fácil **“romper”** algo si ya sabemos cómo construirlo.

### Jerarquía de Active Directory

Active Directory se organiza en una estructura jerárquica en forma de árbol, con un **bosque (forest)** en la parte superior que contiene uno o más dominios, los cuales pueden tener subdominios anidados.

* Un **forest** es el límite de seguridad dentro del cual todos los objetos están bajo control administrativo.
* Un forest puede contener múltiples dominios.
* Un **domain** es la estructura en la que se encuentran objetos (usuarios, computadoras y grupos) accesibles.

Los dominios tienen muchas **Unidades Organizativas (OUs)** integradas, como **Domain Controllers**, **Users**, **Computers**, y pueden crearse nuevas OUs según sea necesario. Las OUs pueden contener objetos y sub-OUs, lo que permite asignar distintas políticas de grupo.

#### Ejemplo de estructura de AD (a nivel muy básico):

```plaintext
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
│               └── barbara.jones
├── CORP.INLANEFREIGHT.LOCAL
└── DEV.INLANEFREIGHT.LOCAL
```

En este ejemplo:

* **INLANEFREIGHT.LOCAL** es el **dominio raíz** que contiene los subdominios (ya sean dominios hijos o dominios raíz de árbol):
  * **ADMIN.INLANEFREIGHT.LOCAL**
  * **CORP.INLANEFREIGHT.LOCAL**
  * **DEV.INLANEFREIGHT.LOCAL**

También incluye otros objetos que componen un dominio, como usuarios, grupos y computadoras.

Es común ver **múltiples dominios (o bosques) vinculados mediante relaciones de confianza** en organizaciones que realizan muchas adquisiciones. A menudo es más rápido y sencillo crear una relación de confianza con otro dominio/forest que recrear todos los usuarios en el dominio actual.

Sin embargo, como veremos más adelante, **las relaciones de confianza pueden introducir muchos problemas de seguridad si no se administran correctamente.**

### Ejemplo de diagrama de bosques y dominios

<figure><img src="../.gitbook/assets/ilflog2.png" alt=""><figcaption></figcaption></figure>

El siguiente gráfico conceptual describe dos bosques:

* **INLANEFREIGHT.LOCAL**
* **FREIGHTLOGISTICS.LOCAL**

El doble sentido de la flecha indica una **relación de confianza bidireccional** entre los dos bosques, lo que significa que los usuarios de INLANEFREIGHT.LOCAL pueden acceder a recursos en FREIGHTLOGISTICS.LOCAL y viceversa.

También se pueden ver múltiples dominios hijos bajo cada dominio raíz.

En este ejemplo:

* El **dominio raíz** confía en sus dominios hijos.
* Pero **los dominios hijos de un bosque no necesariamente tienen confianza establecida con los dominios hijos del otro bosque**.

Esto significa que un usuario de **admin.dev.freightlogistics.local** **NO** podría autenticarse en máquinas de **wh.corp.inlanefreight.local** de forma predeterminada, a pesar de la relación de confianza bidireccional entre los dominios raíz.

Para permitir esa comunicación directa, habría que configurar otra **relación de confianza**.

## Terminología de Active Directory

Antes de profundizar más, vamos a dar un paso atrás y definir algunos términos clave que se usarán en este módulo y en general al trabajar con Active Directory.

### Objeto

Un **objeto** es **cualquier recurso presente en un entorno de Active Directory**, como OUs, impresoras, usuarios, controladores de dominio, etc.

### Atributos

Cada objeto en Active Directory tiene un conjunto de **atributos** asociados que definen sus características.\
Por ejemplo, un objeto de computadora tiene atributos como el nombre del host y el nombre DNS.\
Todos los atributos en AD tienen un nombre LDAP asociado que se usa en consultas LDAP, como `displayName` para el nombre completo o `givenName` para el primer nombre.

### Esquema (Schema)

El **esquema de Active Directory** es el plano o blueprint de todo el entorno.\
Define qué tipos de objetos pueden existir en la base de datos de AD y sus atributos asociados.

* Por ejemplo, los usuarios pertenecen a la clase **"user"** y las computadoras a **"computer"**.
* Cada objeto tiene información obligatoria u opcional almacenada en atributos.
* Cuando se crea un objeto a partir de una clase, se llama **instanciación** y ese objeto es una **instancia** de la clase.

Ejemplo: la computadora **RDS01** es una instancia de la clase **computer**.

### Dominio

Un **dominio** es un **grupo lógico de objetos** como computadoras, usuarios, OUs, grupos, etc.\
Podemos pensarlo como **una ciudad** dentro de un estado o país.\
Los dominios pueden funcionar de forma independiente o conectarse mediante relaciones de confianza (**trusts**).

### Bosque (Forest)

Un **bosque** es un **conjunto de dominios de Active Directory**.\
Es el **contenedor de más alto nivel** que contiene todos los objetos de AD: dominios, usuarios, grupos, computadoras, GPOs, etc.

Un bosque puede contener uno o más dominios y puede pensarse como un **estado** (en EE.UU.) o un **país** (en la UE).\
Cada bosque opera de forma independiente, pero puede establecer relaciones de confianza con otros bosques.

### Árbol (Tree)

Un **árbol** es un **conjunto de dominios** de AD que comienzan en un dominio raíz único.\
Un bosque es un conjunto de **árboles** de AD.

* Los dominios en un árbol comparten un límite de confianza padre-hijo.
* Dos árboles en el mismo bosque no pueden compartir el mismo namespace.

**Ejemplo:**

* Árbol 1: `inlanefreight.local` → hijo: `corp.inlanefreight.local`
* Árbol 2: `ilfreight.local` → hijo: `corp.ilfreight.local`

Todos los dominios en un árbol comparten un **Global Catalog**.

***

### Contenedor (Container)

Los **objetos contenedor** almacenan otros objetos y tienen una ubicación definida en la jerarquía del subárbol del directorio.

***

### Hoja (Leaf)

Los **objetos hoja** **no contienen** otros objetos y se encuentran **al final de la jerarquía** del subárbol.

***

### Identificador Único Global (GUID)

Un **GUID** es un **valor único de 128 bits** asignado cuando se crea un usuario o grupo en el dominio.

* Es único en toda la empresa, como una dirección MAC.
* Todos los objetos de AD tienen un GUID almacenado en el atributo **ObjectGUID**.

Buscar por **GUID** es la forma más precisa de encontrar un objeto en AD.\
El **ObjectGUID** nunca cambia mientras el objeto exista.

***

### Principales de seguridad (Security Principals)

Son **entidades que el sistema operativo puede autenticar**, como:

* Cuentas de usuario
* Cuentas de computadora
* Procesos o servicios ejecutándose bajo cuentas del dominio

En AD, estos objetos pueden **controlar el acceso** a recursos dentro del dominio.\
También existen cuentas locales (no gestionadas por AD) controladas por **Security Accounts Manager (SAM)**.

***

### Identificador de seguridad (SID)

El **SID** es un identificador único para un principal o grupo de seguridad.

* Es asignado por el controlador de dominio.
* Un SID se usa **una sola vez**.
* Cuando un usuario inicia sesión, se genera un **token de acceso** con su SID y los SIDs de sus grupos.
* Existen SIDs bien conocidos, como el del grupo **Everyone**.

***

### Distinguished Name (DN)

El **DN** describe la **ruta completa** a un objeto en AD.\
Ejemplo:

```
iniCopiarEditarcn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local
```

Indica que **bjones** trabaja en IT y su cuenta está en la OU **Employees**.

***

### Relative Distinguished Name (RDN)

El **RDN** es **un solo componente** del DN.

* Identifica al objeto de forma única en su nivel de jerarquía.
* Ejemplo: **bjones** es el RDN en el DN anterior.
* No pueden existir dos objetos con el mismo RDN en el mismo contenedor padre.

***

### sAMAccountName

El **sAMAccountName** es el **nombre de inicio de sesión** del usuario.

* Debe ser único y tener **20 caracteres o menos**.
* Ejemplo: **bjones**.

***

### userPrincipalName

El **userPrincipalName (UPN)** es otro formato para identificar usuarios en AD:

```
sqlCopiarEditarbjones@inlanefreight.local
```

* No es obligatorio.
* Tiene formato de correo electrónico.

***

### Roles FSMO

Para evitar conflictos entre controladores de dominio (DC):

* Microsoft creó las **Flexible Single Master Operations (FSMO)**.
* Dividen funciones críticas entre varios DCs.

Cinco roles FSMO:

* **Schema Master** (uno por bosque)
* **Domain Naming Master** (uno por bosque)
* **RID Master** (uno por dominio)
* **PDC Emulator** (uno por dominio)
* **Infrastructure Master** (uno por dominio)

Estos roles aseguran **replicación correcta** y **operaciones críticas**.

***

### Catálogo Global (Global Catalog)

Un **Global Catalog (GC)** es un DC que:

* Guarda **copias completas** de su dominio.
* Guarda **copias parciales** de objetos de otros dominios en el bosque.

Permite:

* **Autenticación** (incluye todos los grupos de un usuario).
* **Búsquedas** en todo el bosque desde un solo punto.

***

### Read-Only Domain Controller (RODC)

Un **RODC** tiene:

* **Base de datos AD de solo lectura**.
* No almacena contraseñas (excepto su propia cuenta).
* Evita modificaciones replicadas.
* Incluye DNS de solo lectura.
* Reduce tráfico de replicación.
* Permite separación de roles administrativos.

***

### Replicación

La **replicación** sincroniza cambios entre DCs.

* Gestionada por el servicio **Knowledge Consistency Checker (KCC)**.
* Garantiza consistencia y respaldo en caso de fallos.

***

### Service Principal Name (SPN)

Un **SPN** identifica de forma única una instancia de servicio.

* Usado en autenticación Kerberos.
* Permite a las aplicaciones solicitar servicios sin conocer el nombre de cuenta.

***

### Group Policy Object (GPO)

Los **GPOs** son **colecciones virtuales** de configuraciones de política.

* Cada GPO tiene un **GUID único**.
* Se aplican a **usuarios y computadoras** en el dominio o en OUs específicas.

***

### Access Control List (ACL)

Una **ACL** es una colección **ordenada** de **Access Control Entries (ACEs)** que se aplican a un objeto.

***

### Access Control Entry (ACE)

Cada **ACE**:

* Identifica un **trustee** (usuario, grupo o sesión).
* Define derechos **permitidos**, **denegados** o **auditados**.

***

### Discretionary Access Control List (DACL)

La **DACL**:

* Define qué principales de seguridad tienen **permitido o denegado** el acceso a un objeto.
* Si no existe DACL → **acceso total** para todos.
* Si existe pero está vacía → **acceso denegado** para todos.
* Las ACEs se revisan en orden hasta encontrar un match.

***

### System Access Control List (SACL)

La **SACL** permite a los administradores **auditar intentos de acceso** a objetos protegidos.

* Las ACEs en la SACL definen **qué intentos se registran** en el Event Log.

***

### Fully Qualified Domain Name (FQDN)

El **FQDN** es el **nombre completo** de un host.

* Formato: `[nombre_host].[dominio].[tld]`.
* Permite ubicar objetos en la jerarquía DNS.

Ejemplo:

```
pgsqlCopiarEditarDC01.INLANEFREIGHT.LOCAL
```

***

### Tombstone

Un **tombstone** es un **objeto contenedor** para objetos eliminados en AD.

* El atributo **isDeleted** se establece en **TRUE**.
* Después del **Tombstone Lifetime** (60-180 días), se elimina por completo.
* Sin AD Recycle Bin → el objeto se convierte en **tombstone** y pierde la mayoría de sus atributos.

***

### AD Recycle Bin

Introducido en **Windows Server 2008 R2**.

* Permite recuperar objetos eliminados sin restaurar desde backup.
* Conserva la mayoría de los atributos.
* Por defecto → 60 días de retención.
* Facilita la restauración completa de objetos.

***

### SYSVOL

El **SYSVOL** almacena:

* Archivos públicos del dominio.
* Políticas de sistema, GPOs.
* Scripts de inicio/cierre de sesión.

Se replica entre DCs usando **File Replication Services (FRS)**.

***

### AdminSDHolder

El **AdminSDHolder**:

* Administra las **ACLs** de grupos **protegidos** en AD.
* Contiene el **Security Descriptor** aplicado a miembros de grupos privilegiados.
* El proceso **SDProp** se ejecuta cada hora por defecto para aplicar estos permisos.

***

### dsHeuristics

El atributo **dsHeuristics**:

* Es un **string** en el objeto Directory Service.
* Define configuraciones a nivel de bosque.
* Puede excluir grupos integrados de la lista de grupos protegidos.

***

### adminCount

El atributo **adminCount**:

* Determina si el proceso **SDProp** protege a un usuario.
* Valor **0** o no establecido → no protegido.
* Valor **1** → protegido.

***

### Active Directory Users and Computers (ADUC)

**ADUC** es la consola **GUI** para:

* Administrar usuarios, grupos, computadoras y contactos en AD.
* También puede hacerse vía **PowerShell**.

***

### ADSI Edit

**ADSI Edit** es una herramienta **GUI** más avanzada.

* Permite acceder y modificar cualquier atributo de un objeto.
* Debe usarse con precaución para evitar problemas graves en AD.

***

### sIDHistory

El atributo **sIDHistory**:

* Guarda SIDs anteriores de un objeto.
* Usado en migraciones para mantener permisos.
* Mal configurado → posible vector de ataque.

***

### NTDS.DIT

El archivo **NTDS.DIT**:

* Es el **corazón** de Active Directory.
* Almacena datos como usuarios, grupos, membresías y hashes de contraseñas.
* Ubicación: `C:\Windows\NTDS\`.
* Puede extraerse para ataques **pass-the-hash** o cracking offline.

***

### MSBROWSE

**MSBROWSE**:

* Protocolo antiguo de Microsoft para **servicios de exploración** en redes LAN.
* Mantenía listas de recursos compartidos.
* Hoy está **obsoleto**.

En versiones antiguas:

* `nbtstat -A ip-address` → identificar el Master Browser.
* `nltest` → consultar nombres de Domain Controllers.

Hoy se usa **SMB/CIFS**.
