# Protected File Transfers

Los testers de penetración a menudo obtienen acceso a datos altamente sensibles, como listas de usuarios, credenciales (es decir, descarga del archivo NTDS.dit para descifrar contraseñas fuera de línea) y datos de enumeración que pueden contener información crítica sobre la infraestructura de red y el entorno de Active Directory (AD) de una organización. Por lo tanto, es esencial cifrar estos datos o utilizar conexiones de datos cifradas, como SSH, SFTP y HTTPS. Sin embargo, a veces estas opciones no están disponibles, y se requiere un enfoque diferente.

> **Nota:** A menos que el cliente lo solicite específicamente, no recomendamos exfiltrar datos como información de identificación personal (PII), datos financieros (como números de tarjetas de crédito), secretos comerciales, etc. En su lugar, si se intenta probar los controles de Prevención de Pérdida de Datos (DLP) o protecciones de filtrado de egreso, cree un archivo con datos ficticios que imiten los datos que el cliente intenta proteger.

El cifrado de los datos o archivos antes de su transferencia es necesario para evitar que los datos sean leídos si se interceptan en tránsito. La fuga de datos durante una prueba de penetración podría tener graves consecuencias para el tester de penetración, su empresa y el cliente. Como profesionales de la seguridad de la información, debemos actuar con responsabilidad y tomar todas las medidas para proteger cualquier dato con el que nos encontremos durante una evaluación.

## **Cifrado de Archivos en Windows**

Existen varios métodos para cifrar archivos e información en sistemas Windows. Uno de los métodos más simples es utilizar el script PowerShell **Invoke-AESEncryption.ps1**. Este script es pequeño y proporciona cifrado de archivos y cadenas.

### **Ejemplos de Uso**

**1. Cifrar una Cadena de Texto**

```powershell
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Texto Secreto"
```

**Descripción:**\
Cifra el texto `"Texto Secreto"` y genera una salida en Base64.

**2. Desencriptar una Cadena de Texto**

```powershell
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
```

**Descripción:**\
Desencripta la cadena Base64 `"LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="` y genera la salida en texto plano.

**3. Cifrar un Archivo**

```powershell
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
```

**Descripción:**\
Cifra el archivo `"file.bin"` y genera un archivo cifrado `"file.bin.aes"`.

**4. Desencriptar un Archivo**

```powershell
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
```

**Descripción:**\
Desencripta el archivo `"file.bin.aes"` y genera el archivo original `"file.bin"`.

### **Función Invoke-AESEncryption**

El código completo del script se presenta a continuación:

```powershell
function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,

        [Parameter(Mandatory = $true)]
        [String]$Key,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))

        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "¡Archivo no encontrado!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }

                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                $aesManaged.Dispose()

                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "Archivo cifrado a $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "¡Archivo no encontrado!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "Archivo desencriptado a $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()
        $aesManaged.Dispose()
    }
}
```

### **Transferencia Protegida de Archivos**

Una vez que se transfiere el script al host objetivo, solo es necesario importarlo como un módulo de la siguiente manera:

```powershell
PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1
```

Después de importar el script, se pueden cifrar cadenas o archivos. Este comando crea un archivo cifrado con el mismo nombre que el archivo original pero con la extensión **".aes"**.

**Ejemplo de Cifrado de Archivos:**

```powershell
PS C:\htb> Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
```

**Salida:**

```mathematica
File encrypted to C:\htb\scan-results.txt.aes
```

Es esencial utilizar contraseñas muy fuertes y únicas para el cifrado en cada empresa donde se realice una prueba de penetración. Esto es para evitar que los archivos sensibles se descifren utilizando una única contraseña que pueda haber sido filtrada y crackeada por terceros.

## Cifrado de Archivos en Linux

OpenSSL está frecuentemente incluido en las distribuciones de Linux, y los administradores de sistemas lo utilizan para generar certificados de seguridad, entre otras tareas. OpenSSL también puede utilizarse para enviar archivos de manera similar a **netcat** y para cifrar archivos.

Para cifrar un archivo usando **openssl**, podemos seleccionar diferentes cifrados. Como ejemplo, utilizaremos el cifrado **-aes256**. También podemos sobrescribir el número de iteraciones predeterminado con la opción **-iter 100000** y agregar la opción **-pbkdf2** para usar el algoritmo **Password-Based Key Derivation Function 2**. Al ejecutar el comando, se pedirá proporcionar una contraseña.

### **Cifrado de /etc/passwd con OpenSSL**

```bash
sherlock28@htb[/htb]$ openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
```

**Salida:**

```yaml
enter aes-256-cbc encryption password:                                                         
Verifying - enter aes-256-cbc encryption password:
```

Es importante utilizar una contraseña fuerte y única para evitar ataques de fuerza bruta en caso de que una parte no autorizada obtenga el archivo cifrado.

### **Descifrado de passwd.enc con OpenSSL**

Para descifrar el archivo, se puede usar el siguiente comando:

```bash
sherlock28@htb[/htb]$ openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
```

**Salida:**

```yaml
enter aes-256-cbc decryption password:
```

Es posible utilizar cualquiera de los métodos anteriores para transferir este archivo, pero se recomienda emplear un método de transporte seguro, como **HTTPS**, **SFTP**, o **SSH**. Como siempre, es importante practicar estos ejemplos en entornos de prueba, como **Pwnbox**, reproduciendo los ejemplos de OpenSSL.
