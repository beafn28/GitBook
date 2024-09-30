# John The Ripper

John the Ripper es una herramienta esencial para pentesting utilizada para comprobar la fortaleza de las contraseñas y descifrar contraseñas encriptadas (o hasheadas) utilizando ataques de fuerza bruta o de diccionario. Es un software de código abierto, desarrollado inicialmente para sistemas basados en UNIX y lanzado por primera vez en 1996. Se ha convertido en un pilar entre los profesionales de la seguridad debido a sus diversas capacidades. Se recomienda la variante "Jumbo" para quienes trabajan en el campo de la seguridad, ya que tiene optimizaciones de rendimiento y funciones adicionales, como listas de palabras en varios idiomas y soporte para arquitecturas de 64 bits. Esta versión es más efectiva al descifrar contraseñas con mayor precisión y velocidad.

Con esta herramienta, podemos utilizar varios métodos para convertir diferentes tipos de archivos y hashes en un formato que sea utilizable por John. Además, el software se actualiza regularmente para mantenerse al día con las tendencias y tecnologías de seguridad actuales, asegurando la seguridad de los usuarios.

## Tecnologías de Encriptación

| Tecnología de Encriptación                            | Descripción                                                                                                                |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| UNIX crypt(3)                                         | Crypt(3) es un sistema de encriptación tradicional de UNIX con una clave de 56 bits.                                       |
| Encriptación tradicional basada en DES                | La encriptación basada en DES utiliza el algoritmo Data Encryption Standard para cifrar datos.                             |
| bigcrypt                                              | Bigcrypt es una extensión de la encriptación basada en DES tradicional. Utiliza una clave de 128 bits.                     |
| Encriptación extendida de DES en BSDI                 | Esta es una extensión de la encriptación basada en DES y utiliza una clave de 168 bits.                                    |
| Encriptación basada en MD5 en FreeBSD (Linux y Cisco) | Utiliza el algoritmo MD5 para cifrar datos con una clave de 128 bits.                                                      |
| Encriptación basada en Blowfish de OpenBSD            | Utiliza el algoritmo Blowfish para cifrar datos con una clave de 448 bits.                                                 |
| Kerberos/AFS                                          | Kerberos y AFS son sistemas de autenticación que usan encriptación para garantizar la comunicación segura entre entidades. |
| Windows LM                                            | La encriptación de Windows LM usa el algoritmo DES para cifrar datos con una clave de 56 bits.                             |
| Tripcodes basados en DES                              | Se utilizan para autenticar usuarios basados en el algoritmo DES.                                                          |
| Hashes SHA-crypt                                      | Se utilizan para cifrar datos con una clave de 256 bits y están disponibles en versiones más recientes de Fedora y Ubuntu. |
| Hashes SHA-crypt y SUNMD5 (Solaris)                   | Utilizan los algoritmos SHA-crypt y MD5 para cifrar datos con una clave de 256 bits y están disponibles en Solaris.        |
| ...                                                   | y muchos más.                                                                                                              |

## Métodos de Ataque

### Ataques de Diccionario

Los ataques de diccionario implican el uso de una lista pre-generada de palabras y frases (conocida como diccionario) para intentar descifrar una contraseña. Esta lista de palabras y frases a menudo se adquiere de diversas fuentes, como diccionarios públicos, contraseñas filtradas o incluso compradas a empresas especializadas. El diccionario se utiliza para generar una serie de cadenas que luego se comparan con las contraseñas hasheadas. Si se encuentra una coincidencia, la contraseña se descifra, proporcionando acceso al atacante al sistema y a los datos almacenados en él. Este tipo de ataque es altamente efectivo. Por lo tanto, es esencial tomar las medidas necesarias para garantizar que las contraseñas se mantengan seguras, como utilizar contraseñas complejas y únicas, cambiarlas regularmente y utilizar autenticación de dos factores.

### Ataques de Fuerza Bruta

Los ataques de fuerza bruta consisten en intentar todas las combinaciones posibles de caracteres que podrían formar una contraseña. Este es un proceso extremadamente lento, y solo se recomienda utilizar este método si no hay otras alternativas. También es importante destacar que, cuanto más larga y compleja sea la contraseña, más difícil será de descifrar y más tiempo llevará agotar todas las combinaciones. Por esta razón, se recomienda encarecidamente que las contraseñas tengan al menos 8 caracteres de longitud, con una combinación de letras, números y símbolos.

### Ataques con Tablas Rainbow

Los ataques con tablas rainbow utilizan una tabla precomputada de hashes y sus correspondientes contraseñas en texto claro, lo que es un método mucho más rápido que un ataque de fuerza bruta. Sin embargo, este método está limitado por el tamaño de la tabla rainbow: cuanto más grande sea la tabla, más contraseñas y hashes podrá almacenar. Además, debido a la naturaleza del ataque, no es posible utilizar tablas rainbow para determinar el texto claro de hashes que no estén ya incluidos en la tabla. Como resultado, los ataques con tablas rainbow solo son efectivos contra hashes ya presentes en la tabla; cuanto más grande sea la tabla, mayor será el éxito del ataque.

## Modos de Cracking

El _Modo de Cracking Simple_ es uno de los modos más comunes utilizados en John cuando se intenta descifrar contraseñas utilizando una lista de contraseñas única. Es un ataque de fuerza bruta, lo que significa que todas las contraseñas de la lista se prueban, una por una, hasta que se encuentra la correcta. Este método es el más básico y directo para descifrar contraseñas y, por lo tanto, es una opción popular para aquellos que desean acceder a un sistema seguro. Sin embargo, está lejos de ser el método más eficiente, ya que puede llevar un tiempo indefinido descifrar una contraseña, dependiendo de la longitud y complejidad de la misma. La sintaxis básica para el comando es:

```bash
john --format=<tipo_hash> <hash o archivo_de_hashes>
```

Por ejemplo, si tenemos un archivo llamado `hashes_to_crack.txt` que contiene hashes SHA-256, el comando para descifrarlos sería:

```bash
john --format=sha256 hashes_to_crack.txt
```

* `john` es el comando para ejecutar el programa John the Ripper.
* `--format=sha256` especifica que el formato del hash es SHA-256.
* `hashes.txt` es el nombre del archivo que contiene los hashes que se van a descifrar.

Cuando ejecutamos el comando, John leerá los hashes del archivo especificado y luego intentará descifrarlos comparándolos con las palabras de su lista de palabras incorporada y cualquier lista adicional especificada con la opción `--wordlist`. Además, utilizará cualquier regla establecida con la opción `--rules` (si se han dado reglas) para generar posibles contraseñas.

El proceso de descifrar contraseñas puede ser muy lento, ya que la cantidad de tiempo necesaria depende de múltiples factores, como la complejidad de la contraseña, la configuración de la máquina y el tamaño de la lista de palabras. El descifrado de contraseñas es casi una cuestión de suerte. Porque aunque la contraseña sea simple, si usamos una lista incorrecta donde la palabra no está presente o no puede ser generada por John, eventualmente fallaremos en descifrar la contraseña.

John enviará las contraseñas descifradas a la consola y al archivo "john.pot" (`~/.john/john.pot`) en el directorio del usuario actual. Además, continuará descifrando los hashes restantes en segundo plano, y podemos verificar el progreso ejecutando el comando `john --show`. Para maximizar las posibilidades de éxito, es importante asegurarse de que las listas de palabras y reglas utilizadas sean completas y estén actualizadas.

### Cracking con John the Ripper

| **Formato de Hash**        | **Comando de Ejemplo**                                 | **Descripción**                                                        |
| -------------------------- | ------------------------------------------------------ | ---------------------------------------------------------------------- |
| AFS                        | `john --format=afs hashes_a_descifrar.txt`             | Hashes de contraseñas del AFS (Sistema de Archivos de Andrew)          |
| bfegg                      | `john --format=bfegg hashes_a_descifrar.txt`           | Hashes bfegg utilizados en bots de Eggdrop en IRC                      |
| bf                         | `john --format=bf hashes_a_descifrar.txt`              | Hashes basados en Blowfish (crypt(3))                                  |
| bsdi                       | `john --format=bsdi hashes_a_descifrar.txt`            | Hashes de BSDi (crypt(3))                                              |
| crypt(3)                   | `john --format=crypt hashes_a_descifrar.txt`           | Hashes tradicionales de Unix (crypt(3))                                |
| des                        | `john --format=des hashes_a_descifrar.txt`             | Hashes tradicionales basados en DES (crypt(3))                         |
| dmd5                       | `john --format=dmd5 hashes_a_descifrar.txt`            | Hashes DMD5 (MD5 de Dragonfly BSD)                                     |
| dominosec                  | `john --format=dominosec hashes_a_descifrar.txt`       | Hashes de contraseñas de IBM Lotus Domino 6/7                          |
| Hashes de SID de EPiServer | `john --format=episerver hashes_a_descifrar.txt`       | Hashes de contraseñas de SID (Identificador de Seguridad) de EPiServer |
| hdaa                       | `john --format=hdaa hashes_a_descifrar.txt`            | Hashes de contraseñas hdaa utilizados en Openwall GNU/Linux            |
| hmac-md5                   | `john --format=hmac-md5 hashes_a_descifrar.txt`        | Hashes de contraseñas hmac-md5                                         |
| hmailserver                | `john --format=hmailserver hashes_a_descifrar.txt`     | Hashes de contraseñas de hmailserver                                   |
| ipb2                       | `john --format=ipb2 hashes_a_descifrar.txt`            | Hashes de contraseñas de Invision Power Board 2                        |
| krb4                       | `john --format=krb4 hashes_a_descifrar.txt`            | Hashes de contraseñas Kerberos 4                                       |
| krb5                       | `john --format=krb5 hashes_a_descifrar.txt`            | Hashes de contraseñas Kerberos 5                                       |
| LM                         | `john --format=LM hashes_a_descifrar.txt`              | Hashes de contraseñas LM (Lan Manager)                                 |
| lotus5                     | `john --format=lotus5 hashes_a_descifrar.txt`          | Hashes de contraseñas de Lotus Notes/Domino 5                          |
| mscash                     | `john --format=mscash hashes_a_descifrar.txt`          | Hashes de contraseñas de MS Cache                                      |
| mscash2                    | `john --format=mscash2 hashes_a_descifrar.txt`         | Hashes de contraseñas de MS Cache v2                                   |
| mschapv2                   | `john --format=mschapv2 hashes_a_descifrar.txt`        | Hashes de contraseñas de MS CHAP v2                                    |
| mskrb5                     | `john --format=mskrb5 hashes_a_descifrar.txt`          | Hashes de contraseñas de MS Kerberos 5                                 |
| mssql05                    | `john --format=mssql05 hashes_a_descifrar.txt`         | Hashes de contraseñas de MS SQL 2005                                   |
| mssql                      | `john --format=mssql hashes_a_descifrar.txt`           | Hashes de contraseñas de MS SQL                                        |
| mysql-fast                 | `john --format=mysql-fast hashes_a_descifrar.txt`      | Hashes de contraseñas rápidas de MySQL                                 |
| mysql                      | `john --format=mysql hashes_a_descifrar.txt`           | Hashes de contraseñas de MySQL                                         |
| mysql-sha1                 | `john --format=mysql-sha1 hashes_a_descifrar.txt`      | Hashes de contraseñas SHA1 de MySQL                                    |
| NETLM                      | `john --format=netlm hashes_a_descifrar.txt`           | Hashes de contraseñas NETLM (NT LAN Manager)                           |
| NETLMv2                    | `john --format=netlmv2 hashes_a_descifrar.txt`         | Hashes de contraseñas NETLMv2                                          |
| NETNTLM                    | `john --format=netntlm hashes_a_descifrar.txt`         | Hashes de contraseñas NETNTLM (NT LAN Manager)                         |
| NETNTLMv2                  | `john --format=netntlmv2 hashes_a_descifrar.txt`       | Hashes de contraseñas NETNTLMv2                                        |
| NEThalfLM                  | `john --format=nethalflm hashes_a_descifrar.txt`       | Hashes de contraseñas NEThalfLM                                        |
| md5ns                      | `john --format=md5ns hashes_a_descifrar.txt`           | Hashes de contraseñas md5ns (MD5 namespace)                            |
| nsldap                     | `john --format=nsldap hashes_a_descifrar.txt`          | Hashes de contraseñas nsldap (OpenLDAP SHA)                            |
| ssha                       | `john --format=ssha hashes_a_descifrar.txt`            | Hashes de contraseñas ssha (Salted SHA)                                |
| NT                         | `john --format=nt hashes_a_descifrar.txt`              | Hashes de contraseñas NT (Windows NT)                                  |
| openssha                   | `john --format=openssha hashes_a_descifrar.txt`        | Hashes de contraseñas de clave privada OPENSSH                         |
| oracle11                   | `john --format=oracle11 hashes_a_descifrar.txt`        | Hashes de contraseñas de Oracle 11                                     |
| oracle                     | `john --format=oracle hashes_a_descifrar.txt`          | Hashes de contraseñas de Oracle                                        |
| pdf                        | `john --format=pdf hashes_a_descifrar.txt`             | Hashes de contraseñas PDF (Formato de Documento Portátil)              |
| phpass-md5                 | `john --format=phpass-md5 hashes_a_descifrar.txt`      | Hashes de PHPass-MD5 (framework de hashing de contraseñas PHP)         |
| phps                       | `john --format=phps hashes_a_descifrar.txt`            | Hashes de contraseñas PHPS                                             |
| pix-md5                    | `john --format=pix-md5 hashes_a_descifrar.txt`         | Hashes de contraseñas MD5 de Cisco PIX                                 |
| po                         | `john --format=po hashes_a_descifrar.txt`              | Hashes de contraseñas Po (Sybase SQL Anywhere)                         |
| rar                        | `john --format=rar hashes_a_descifrar.txt`             | Hashes de contraseñas RAR (WinRAR)                                     |
| raw-md4                    | `john --format=raw-md4 hashes_a_descifrar.txt`         | Hashes de contraseñas Raw MD4                                          |
| raw-md5                    | `john --format=raw-md5 hashes_a_descifrar.txt`         | Hashes de contraseñas Raw MD5                                          |
| raw-md5-unicode            | `john --format=raw-md5-unicode hashes_a_descifrar.txt` | Hashes de contraseñas Raw MD5 Unicode                                  |
| raw-sha1                   | `john --format=raw-sha1 hashes_a_descifrar.txt`        | Hashes de contraseñas Raw SHA1                                         |
| raw-sha224                 | `john --format=raw-sha224 hashes_a_descifrar.txt`      | Hashes de contraseñas Raw SHA224                                       |
| raw-sha256                 | `john --format=raw-sha256 hashes_a_descifrar.txt`      | Hashes de contraseñas Raw SHA256                                       |
| raw-sha384                 | `john --format=raw-sha384 hashes_a_descifrar.txt`      | Hashes de contraseñas Raw SHA384                                       |
| raw-sha512                 | `john --format=raw-sha512 hashes_a_descifrar.txt`      | Hashes de contraseñas Raw SHA512                                       |
| salted-sha                 | `john --format=salted-sha hashes_a_descifrar.txt`      | Hashes de contraseñas Salted SHA                                       |
| sapb                       | `john --format=sapb hashes_a_descifrar.txt`            | Hashes de contraseñas SAP CODVN B (BCODE)                              |
| sapg                       | `john --format=sapg hashes_a_descifrar.txt`            | Hashes de contraseñas SAP CODVN G (PASSCODE)                           |
| sha1-gen                   | `john --format=sha1-gen hashes_a_descifrar.txt`        | Hashes de contraseñas SHA1 genérico                                    |
| sha1-unix                  | `john --format=sha1-unix hashes_a_descifrar.txt`       | Hashes de contraseñas SHA1 de UNIX                                     |
| sha1-ntlm                  | `john --format=sha1-ntlm hashes_a_descifrar.txt`       | Hashes de contraseñas SHA1 NTLM (NT LAN Manager)                       |
| sha256-unix                | `john --format=sha256-unix hashes_a_descifrar.txt`     | Hashes de contraseñas SHA256 de UNIX                                   |
| sha256-ntlm                | `john --format=sha256-ntlm hashes_a_descifrar.txt`     | Hashes de contraseñas SHA256 NTLM (NT LAN Manager)                     |
| sha512-unix                | `john --format=sha512-unix hashes_a_descifrar.txt`     | Hashes de contraseñas SHA512 de UNIX                                   |
| xsha1                      | `john --format=xsha1 hashes_a_descifrar.txt`           | Hashes de contraseñas XSHA1                                            |
| zip                        | `john --format=zip hashes_a_descifrar.txt`             | Hashes de contraseñas ZIP                                              |

### Modo de Lista de Palabras

Este modo utiliza una lista de palabras para intentar descifrar las contraseñas. La sintaxis básica del comando es:

```bash
john --wordlist=<ruta_a_lista_de_palabras> <archivo_de_hashes>
```

### Modo Incremental

En este modo, John generará combinaciones de caracteres en lugar de solo intentar coincidirlas. Este método es más costoso computacionalmente y más lento que el Modo de Lista de Palabras. La sintaxis básica del comando es:

```bash
john --incremental <archivo_de_hashes>
```

### Modo de Cracking Único

El Modo de Cracking Único está diseñado para descifrar contraseñas basándose en las propiedades del hash de la contraseña. La sintaxis básica del comando es:

```bash
john --single <archivo_de_hashes>
```

### Modo Externo

El Modo Externo es donde los usuarios pueden definir sus reglas para descifrar contraseñas. Este modo es versátil y puede adaptarse a casos específicos donde se requiere un ataque particular. La sintaxis básica del comando es:

```bash
john --external=<regla_externa> <archivo_de_hashes>
```

## Comandos de Ejemplo para Cracking

1. **Usando Modo de Lista de Palabras**: Para descifrar un hash almacenado en `hashes.txt` usando una lista de palabras llamada `mi_lista_de_palabras.txt`, ejecutarías:

```bash
john --wordlist=mi_lista_de_palabras.txt --rules hashes.txt
```

2. **Usando Modo Incremental**: Para descifrar hashes usando el conjunto de caracteres predeterminado, ejecutarías:

```bash
john --incremental hashes.txt
```

3. **Usando Modo de Cracking Único**: Para intentar un descifrado básico en un hash, ejecutarías:

```bash
john --single hashes.txt
```

4. **Usando Modo Externo**: Para utilizar un modo externo personalizado llamado `mi_regla_externa`, ejecutarías:

```bash
john --external=mi_regla_externa hashes.txt
```

5. **Descifrado de Formato de Hash Específico**: Si necesitas descifrar un formato de hash específico como NTLM, especificarías el formato con:

```bash
john --format=nt hashes.txt
```

John the Ripper guarda las contraseñas descifradas en un archivo llamado `john.pot` por defecto. Puedes especificar un archivo de salida diferente usando la opción `--pot=<nombre_de_archivo>`. Por ejemplo:

```bash
john --pot=mi_salida_personalizada.pot hashes.txt
```

Para ver las contraseñas descifradas, puedes usar:

```bash
john --show hashes.txt
```

* **Modo Verboso**: Agrega `-v` para salida detallada.
* **Control de Sesión**: Usa `--session=<nombre_de_sesión>` para guardar el progreso y reanudarlo más tarde.
* **Verificación de Estado**: Usa `--status` para verificar el estado actual de una sesión en ejecución.

### Script de PowerShell para Crear Hashes

Este script de PowerShell genera diferentes tipos de hashes de contraseñas, mostrando MD5, SHA1, SHA256 y BCrypt.

```powershell
# Script de PowerShell para generar hashes de contraseñas

# Función para crear hash MD5
function Create-MD5Hash {
    param(
        [string]$inputString
    )
    $md5 = [System.Security.Cryptography.MD5]::Create()
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($inputString)
    $hash = $md5.ComputeHash($bytes)
    return [BitConverter]::ToString($hash) -replace '-', ''
}

# Función para crear hash SHA1
function Create-SHA1Hash {
    param(
        [string]$inputString
    )
    $sha1 = [System.Security.Cryptography.SHA1]::Create()
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($inputString)
    $hash = $sha1.ComputeHash($bytes)
    return [BitConverter]::ToString($hash) -replace '-', ''
}

# Función para crear hash SHA256
function Create-SHA256Hash {
    param(
        [string]$inputString
    )
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($inputString)
    $hash = $sha256.ComputeHash($bytes)
    return [BitConverter]::ToString($hash) -replace '-', ''
}

# Función para crear hash BCrypt
function Create-BCryptHash {
    param(
        [string]$inputString
    )
    Add-Type -AssemblyName "BCrypt.Net"
    $hash = [BCrypt.Net.BCrypt]::HashPassword($inputString)
    return $hash
}

# Ejemplo de uso
$password = "MiContraseñaSegura"
$md5Hash = Create-MD5Hash -inputString $password
$sha1Hash = Create-SHA1Hash -inputString $password
$sha256Hash = Create-SHA256Hash -inputString $password
$bcryptHash = Create-BCryptHash -inputString $password

# Resultados de salida
Write-Host "MD5: $md5Hash"
Write-Host "SHA1: $sha1Hash"
Write-Host "SHA256: $sha256Hash"
Write-Host "BCrypt: $bcryptHash"
```

#### Cómo Usar

1. **Abrir PowerShell**: Ejecuta PowerShell como administrador.
2. **Copiar y Pegar**: Copia todo el script anterior y pégalo en la consola de PowerShell.
3. **Ejecutar**: Presiona `Enter` para ejecutar el script.
4. **Ver Resultados**: Los hashes de contraseñas se mostrarán en la salida de la consola.

#### Funciones de Hash

* **MD5**: Produce un valor hash de 128 bits, generalmente expresado como un número hexadecimal de 32 caracteres. Es rápido, pero no se recomienda para aplicaciones sensibles a la seguridad debido a vulnerabilidades.
* **SHA1**: Produce un valor hash de 160 bits, típicamente representado como un número hexadecimal de 40 caracteres. También se considera débil frente a ataques.
* **SHA256**: Parte de la familia SHA-2, produce un valor hash de 256 bits y se considera seguro para muchas aplicaciones.
* **BCrypt**: Una función de hash de contraseñas diseñada para seguridad, es lenta y adaptable, lo que la hace más resistente a ataques de fuerza bruta.

## Cracking Archivos con John the Ripper

Es posible descifrar archivos protegidos por contraseña o cifrados utilizando John the Ripper. Para ello, se utilizan herramientas adicionales que procesan los archivos dados y producen hashes que John puede trabajar. John detecta automáticamente los formatos y trata de descifrarlos. La sintaxis para esto puede verse así:

### Cracking Archivos con John

```bash
cry0l1t3@htb:~$ <tool> <file_to_crack> > file.hash
cry0l1t3@htb:~$ pdf2john server_doc.pdf > server_doc.hash
cry0l1t3@htb:~$ john server_doc.hash
# O
cry0l1t3@htb:~$ john --wordlist=<wordlist.txt> server_doc.hash
```

Además, podemos usar diferentes modos con nuestras listas de palabras personales y reglas. Hemos creado una lista que incluye muchas, pero no todas, las herramientas que se pueden usar para John:

### Herramientas para John the Ripper

| **Herramienta**            | **Descripción**                                    |
| -------------------------- | -------------------------------------------------- |
| **pdf2john**               | Convierte documentos PDF para John                 |
| **ssh2john**               | Convierte claves privadas SSH para John            |
| **mscash2john**            | Convierte hashes de MS Cash para John              |
| **keychain2john**          | Convierte archivos de llavero de OS X para John    |
| **rar2john**               | Convierte archivos RAR para John                   |
| **pfx2john**               | Convierte archivos PKCS#12 para John               |
| **truecrypt\_volume2john** | Convierte volúmenes de TrueCrypt para John         |
| **keepass2john**           | Convierte bases de datos de KeePass para John      |
| **vncpcap2john**           | Convierte archivos PCAP de VNC para John           |
| **putty2john**             | Convierte claves privadas de PuTTY para John       |
| **zip2john**               | Convierte archivos ZIP para John                   |
| **hccap2john**             | Convierte capturas de handshake WPA/WPA2 para John |
| **office2john**            | Convierte documentos de MS Office para John        |
| **wpa2john**               | Convierte handshakes WPA/WPA2 para John            |

Más de estas herramientas se pueden encontrar en Pwnbox de la siguiente manera:

```bash
sherlock28@htb[/htb]$ locate *2john*
```

Esto devolverá una lista de herramientas disponibles para la conversión de diversos formatos de archivos que John puede procesar:

```plaintext
/usr/bin/bitlocker2john
/usr/bin/dmg2john
/usr/bin/gpg2john
/usr/bin/hccap2john
/usr/bin/keepass2john
/usr/bin/putty2john
/usr/bin/racf2john
/usr/bin/rar2john
/usr/bin/uaf2john
/usr/bin/vncpcap2john
/usr/bin/wlanhcx2john
/usr/bin/wpapcap2john
/usr/bin/zip2john
/usr/share/john/1password2john.py
/usr/share/john/7z2john.pl
/usr/share/john/DPAPImk2john.py
/usr/share/john/adxcsouf2john.py
/usr/share/john/aem2john.py
/usr/share/john/aix2john.pl
/usr/share/john/aix2john.py
/usr/share/john/andotp2john.py
/usr/share/john/androidbackup2john.py
...SNIP...
```
