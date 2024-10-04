# Passwd, Shadow & Opasswd

Las distribuciones basadas en Linux pueden usar diferentes mecanismos de autenticación. Uno de los más comunes y estandarizados es el uso de **Módulos de Autenticación Enchufables** (PAM, por sus siglas en inglés). Los módulos utilizados para esto se llaman `pam_unix.so` o `pam_unix2.so` y están ubicados en `/usr/lib/x86_x64-linux-gnu/security/` en las distribuciones basadas en Debian. Estos módulos administran la información del usuario, la autenticación, las sesiones, las contraseñas actuales y las antiguas. Por ejemplo, si queremos cambiar la contraseña de nuestra cuenta en el sistema Linux con el comando `passwd`, PAM es invocado, toma las precauciones necesarias y gestiona la información de manera adecuada.

El módulo estándar **`pam_unix.so`** utiliza llamadas API estandarizadas de las bibliotecas del sistema y archivos para actualizar la información de la cuenta. Los archivos estándar que se leen, administran y actualizan son **`/etc/passwd`** y **`/etc/shadow`**. PAM también tiene muchos otros módulos de servicio, como LDAP, mount o Kerberos.

## Archivo `/etc/passwd`

El archivo **`/etc/passwd`** contiene información sobre todos los usuarios existentes en el sistema y puede ser leído por todos los usuarios y servicios. Cada entrada en este archivo identifica un usuario del sistema. Cada entrada tiene siete campos, separados por dos puntos (`:`), que contienen información específica del usuario. Una entrada típica se vería así:

```plaintext
cry0l1t3:x:1000:1000:cry0l1t3,,,:/home/cry0l1t3:/bin/bash
```

### **Formato del archivo `/etc/passwd`**:

| Campo                          | Descripción                  |
| ------------------------------ | ---------------------------- |
| Nombre de inicio de sesión     | Información de contraseña    |
| UID (Identificador de usuario) | GID (Identificador de grupo) |
| Nombre completo/comentarios    | Directorio principal         |
| Shell de inicio                |                              |

Uno de los campos más interesantes es el de **información de contraseña**, ya que puede contener diferentes entradas. En sistemas antiguos, este campo podría contener el hash de la contraseña cifrada. Sin embargo, en los sistemas modernos, los valores de hash se almacenan en el archivo **`/etc/shadow`**.

### **Editando `/etc/passwd`**

En condiciones normales, encontramos el valor `x` en este campo, lo que significa que las contraseñas se almacenan en forma cifrada en **`/etc/shadow`**. Sin embargo, en algunos casos, el archivo **`/etc/passwd`** puede ser escribible por error, lo que permitiría eliminar la información de la contraseña para el usuario `root`, permitiendo iniciar sesión sin que se solicite una contraseña.

**Antes de editar**:

```plaintext
root:x:0:0:root:/root:/bin/bash
```

**Después de editar**:

```plaintext
root::0:0:root:/root:/bin/bash
```

Esto causaría que el sistema no solicite una contraseña cuando alguien intente iniciar sesión como `root`.

Aunque estos casos son raros, es importante estar atentos a brechas de seguridad, especialmente cuando se configuran permisos para directorios completos.

## Archivo `/etc/shadow`

El archivo **`/etc/shadow`** fue desarrollado para aumentar la seguridad del almacenamiento de contraseñas, ya que contiene toda la información relacionada con las contraseñas de los usuarios. Este archivo es solo legible por usuarios con permisos de administrador. Su formato es similar al de **`/etc/passwd`**, pero dividido en nueve campos:

```plaintext
cry0l1t3:$6$wBRzy$...SNIP...x9cDWUxW1:18937:0:99999:7:::
```

### **Formato del archivo `/etc/shadow`**:

| Campo                        | Descripción                  |
| ---------------------------- | ---------------------------- |
| Nombre de usuario            | Contraseña cifrada           |
| Último cambio de contraseña  | Edad mínima de la contraseña |
| Edad máxima de la contraseña | Período de advertencia       |
| Período de inactividad       | Fecha de expiración          |
| No utilizado                 |                              |

Si el campo de la contraseña contiene caracteres como `!` o `*`, el usuario no puede iniciar sesión con una contraseña de Unix, aunque podrían usarse otros métodos de autenticación (Kerberos, autenticación basada en claves, etc.). Si el campo de la contraseña está vacío, el inicio de sesión no requiere una contraseña.

### **Algoritmos de cifrado**

El formato de las contraseñas cifradas es el siguiente:

```plaintext
$<tipo>$<sal>$<hash>
```

Los tipos de cifrado disponibles incluyen:

* **$1$** – MD5
* **$2a$** – Blowfish
* **$2y$** – Eksblowfish
* **$5$** – SHA-256
* **$6$** – SHA-512 (predeterminado en sistemas modernos)

## Archivo `/etc/security/opasswd`

La biblioteca PAM puede evitar el uso de contraseñas antiguas mediante el archivo **`/etc/security/opasswd`**, que requiere permisos de administrador para ser leído. Este archivo almacena contraseñas antiguas, y su formato es similar al de los archivos `passwd` y `shadow`.

## Cracking de Credenciales en Linux

Una vez que hemos recopilado algunos hashes, podemos intentar descifrarlos utilizando herramientas como **Hashcat**.

### **Combinando archivos `passwd` y `shadow`**

```bash
sudo cp /etc/passwd /tmp/passwd.bak
sudo cp /etc/shadow /tmp/shadow.bak
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```

### **Usando Hashcat para crackear hashes**

```bash
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

### **Crackeando hashes MD5**

```bash
hashcat -m 500 -a 0 md5-hashes.list rockyou.txt
```

#### Conclusión

El correcto manejo y protección de archivos como **`/etc/passwd`**, **`/etc/shadow`** y **`/etc/security/opasswd`** es crucial para mantener la seguridad de un sistema Linux, ya que cualquier vulnerabilidad en estos archivos puede ser explotada para obtener acceso no autorizado.
