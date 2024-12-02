# Krypton

### NIVEL 0

¡Bienvenido a Krypton! El primer nivel es fácil. La siguiente cadena codifica la contraseña usando Base64:

`S1JZUFRPTklTR1JFQVQ=`

Utiliza esta contraseña para iniciar sesión en `krypton.labs.overthewire.org` con el nombre de usuario `krypton1` usando SSH en el puerto 2231. Puedes encontrar los archivos para otros niveles en `/krypton/`.

<figure><img src="../../.gitbook/assets/image (430).png" alt=""><figcaption></figcaption></figure>

### NIVEL 1

Ingresa las credenciales para el Nivel 1:

* **Usuario:** `krypton1`
* **Contraseña:** `KRYPTONISGREAT`

<figure><img src="../../.gitbook/assets/image (431).png" alt=""><figcaption></figcaption></figure>

La contraseña para el nivel 2 está en el archivo `krypton2`. Está **encriptada** usando una rotación simple y también en un formato de cifrado no estándar. Cuando se utilizan caracteres alfabéticos para el texto cifrado, es común agrupar las letras en bloques de **5 letras**, independientemente de los límites de las palabras. Esto ayuda a ocultar patrones. Este archivo ha conservado los límites de las palabras del texto plano y los ha trasladado al texto cifrado.

<figure><img src="../../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>

Para descifrar el texto cifrado en **ROT13** usando el archivo `krypton2`, puedes usar el siguiente comando:

```bash
cat krypton2 | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

<figure><img src="../../.gitbook/assets/image (433).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (434).png" alt=""><figcaption></figcaption></figure>

### NIVEL 2

Ingresa las credenciales para el Nivel 2:

* **Usuario:** `krypton2`
* **Contraseña:** `ROTTEN`

<figure><img src="../../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (437).png" alt=""><figcaption></figcaption></figure>

Primero, necesitamos crear un directorio temporal para nuestro trabajo. Puedes hacerlo con el siguiente comando:

```bash
mktemp -d
```

Esto te proporcionará una ruta que se verá algo así como:

```plaintext
/tmp/tmp.GHN79NW8Cy
```

`GHN79NW8Cy` es un nombre aleatorio y será diferente para ti. Utiliza esta ruta para tus archivos temporales. Cambia al directorio creado con:

```bash
cd /tmp/tmp.GHN79NW8Cy
```

Necesitamos crear un enlace simbólico para el archivo `keyfile.dat`, porque el binario `encrypt` busca el archivo de clave en el directorio actual. Ejecuta el siguiente comando:

```bash
ln -s /krypton/krypton2/keyfile.dat
```

Luego, usa `ls` para verificar que el enlace simbólico se ha creado correctamente en tu directorio temporal.

<figure><img src="../../.gitbook/assets/image (438).png" alt=""><figcaption></figcaption></figure>

Asegúrate de tener los permisos necesarios en el directorio actual ejecutando:

```bash
chmod 777 .
```

Esto garantiza que el binario `encrypt` pueda acceder a los archivos en el directorio.

El contenido que necesitamos recuperar se encuentra en el archivo `/krypton/krypton2/krypton3`. Puedes descifrarlo usando:

```bash
cat /krypton/krypton2/krypton3 | tr a-zA-Z o-za-nO-ZA-N
```

Esto aplicará un cifrado César para obtener el texto plano.

<figure><img src="../../.gitbook/assets/image (439).png" alt=""><figcaption></figcaption></figure>

### NIVEL 3

Ingresa las credenciales para el Nivel 3:

* **Usuario:** `krypton3`
* **Contraseña:** `CAESARISEASY`

<figure><img src="../../.gitbook/assets/image (440).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (441).png" alt=""><figcaption></figcaption></figure>

Este proceso se basa en un cifrado por sustitución, cuyo objetivo es descifrar un mensaje cifrado. Para lograrlo, se implementaron dos pasos fundamentales:

#### 1. Análisis de Trigramas

En lugar de analizar la frecuencia de letras individuales, se realizó un análisis de **trigramas**, es decir, de secuencias de tres letras consecutivas. Este método es más efectivo porque los trigramas reflejan patrones más comunes y estables en un idioma, facilitando la identificación de palabras frecuentes y proporcionando un punto de partida más sólido para el descifrado.

Los trigramas más comunes en inglés son:

* **"the"** (1.81%)
* **"and"** (0.73%)
* **"tha"** (0.33%)
* **"ent"** (0.42%)
* **"ing"** (0.72%)
* **"ion"** (0.42%)
* **"tio"** (0.31%)
* **"for"** (0.34%)

#### 2. Resultados del Análisis de Frecuencia

El análisis de trigramas en el texto cifrado mostró que la secuencia **"JDS"** aparecía 61 veces, lo que supera notablemente a otros trigramas. Esto sugiere que **"JDS"** corresponde al trigrama más común en inglés: **"THE"**.

#### Proceso de Descifrado

A partir de esta observación, se procedió a descifrar el texto aplicando sustituciones progresivas de letras. Este proceso se puede realizar utilizando herramientas en línea o mediante comandos en la línea de comandos de Linux. La sustitución se realiza paso a paso, identificando patrones y palabras potenciales hasta obtener el mensaje completo.

En conclusión, el método de análisis de trigramas y sustituciones sucesivas permitió descifrar el texto cifrado, revelando finalmente la clave del mensaje.

```markdown
cat krypton4 | tr JDS THE
```

**Resultado:**

```
KEVVW BGETH EVEIE VXBMN YQUUK BNWCU ANMTE
```

**Posible versión limpia:**

```
KEVVWBGE THE VEIEVXBMNYQUUKBNWCUANMTE
```

Continuamos con más sustituciones:

```bash
cat krypton4 | tr JDSVI THELV
```

**Resultado:**

```
KELLW BGETH ELEVE LXBMN YQUUK BNWCU ANMTE
```

Finalmente, podemos ver algo como 'level'.\
**Posible versión limpia:**

```
KELLWBGE THE LEVEL XBMNYQUUKBNWCUANMTE
```

Seguimos sustituyendo:

```bash
cat krypton4 | tr JDSVIX THELVF
```

**Resultado:**

```
KELLW BGETH ELEVE LFBMN YQUUK BNWCU ANMTE
```

**Posible versión limpia:**

```
KELLWBGE THE LEVEL FBMNYQUUKBNWCUANMTE
```

Avanzamos:

```bash
cat krypton4 | tr JDSVIXBMN THELVFOUR
```

**Resultado:**

```
KELLW OGETH ELEVE LFOUR YQUUK ORWCU ARUTE
```

Ahora, parece que dice 'level four'.\
**Posible versión limpia:**

```
KELLWOGE THE LEVEL FOUR YQUUKORWCUARUTE
```

Seguimos con más sustituciones:

```bash
cat krypton4 | tr JDSVIXBMNK THELVFOURW
```

**Resultado:**

```
WELLW OGETH ELEVE LFOUR YQUUW ORWCU ARUTE
```

El inicio parece algo como 'well done'.\
**Posible versión limpia:**

```
WELL WOGE THE LEVEL FOUR YQUUWORWCUARUTE
```

Continuamos:

```bash
cat krypton4 | tr JDSVIXBMNKW THELVFOURWD
```

**Resultado:**

```
WELLD OGETH ELEVE LFOUR YQUUW ORDCU ARUTE
```

**Posible versión limpia:**

```
WELL DOGE THE LEVEL FOUR YQUUWORDCUARUTE
```

Más sustituciones:

```bash
cat krypton4 | tr JDSVIXBMNKWUG THELVFOURWDSN
```

**Resultado:**

```
WELLD ONETH ELEVE LFOUR YQSSW ORDCS ARUTE
```

La palabra después de 'four' parece 'password'.\
**Posible versión limpia:**

```
WELL DONE THE LEVEL FOUR YQSSWORDCSARUTE
```

Finalmente:

```bash
cat krypton4 | tr JDSVIXBMNKWUGYQ THELVFOURWDSNPA
```

**Resultado:**

```
WELLD ONETH ELEVE LFOUR PASSW ORDCS ARUTE
```

La cadena final desconocida debería ser la contraseña. Hay dos opciones posibles:

1. **'CSARUTE'** es la contraseña parcialmente descifrada.
2. La cadena debería ser algo como: 'WELL DONE THE LEVEL FOUR PASSWORD IS {PASSWORDHERE}'.

Vamos a intentar con el segundo método.

**Posible versión limpia:**

WELL DONE THE LEVEL FOUR PASSWORD CSARUTE

```bash
cat krypton4 | tr JDSVIXBMNKWUGYQS THELVFOURWDSNPAI
```

**Resultado:**

```
WELLD ONETH ELEVE LFOUR PASSW ORDIS ARUTE
```

**Posible versión limpia:**

```vbnet
WELL DONE THE LEVEL FOUR PASSWORD IS ARUTE
```

La contraseña para el nivel 4 es:

**BRUTE**

### NIVEL 4

Ingresa las credenciales para el Nivel 4:

* **Usuario:** `krypton4`
* **Contraseña:** `BRUTE`

<figure><img src="../../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-22 111951.png" alt=""><figcaption></figcaption></figure>

Este nivel se centra en descifrar un cifrado Vigènere, que es una variación del cifrado César, pero con una clave de longitud variable en lugar de un carácter fijo.

El README nos proporciona información interesante, a saber:

* Este nivel utiliza un cifrado de Vigenère.
* La longitud de la clave es 6.

#### Cifrado de Vigenère

El cifrado de Vigenère es una versión avanzada del cifrado César. En lugar de desplazar cada letra por la misma cantidad, se utiliza una clave que codifica la letra 1 con un desplazamiento, la letra 2 con otro desplazamiento, y así sucesivamente.

Después de 6 letras (la longitud de la clave), el patrón se repite, por lo que conocer la longitud de la clave nos ayudará a resolverlo.

#### Texto cifrado de la bandera

El texto cifrado de la bandera es muy corto: `HCIKV RJOX`.

Al igual que en el nivel anterior, cuanto más largo sea el texto cifrado, más precisa será nuestro análisis. Por lo tanto, necesitaremos usar los dos textos cifrados encontrados para determinar la clave.

#### Uso de una Herramienta de Cifrado

Usando una herramienta como [Dcode.fr](https://www.dcode.fr/), copia y pega el texto de `found1` en el sitio web. Introduce `6` como la longitud de la clave.

<figure><img src="../../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (444).png" alt=""><figcaption></figcaption></figure>

Sabiendo la clave he creado este script para obtener la contraseña para el siguiente nivel.

```python
import sys

def decrypt_vigenere(ciphertext, key):
    decrypted_text = []
    key_length = len(key)
    key_int = [ord(i) for i in key]
    text_int = [ord(i) for i in ciphertext]
    for i in range(len(text_int)):
        if text_int[i] >= 65 and text_int[i] <= 90:  # A-Z
            value = (text_int[i] - key_int[i % key_length] + 26) % 26
            decrypted_text.append(chr(value + 65))
        else:
            decrypted_text.append(chr(text_int[i]))
    return ''.join(decrypted_text)

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: python cifrar.py <ciphertext> <key>")
        sys.exit(1)
    ciphertext = sys.argv[1].replace(" ", "").upper()
    key = sys.argv[2].upper()
    print(decrypt_vigenere(ciphertext, key))
```

Ejecutándolo con este comando:

```bash
python3 cifrar.py "HCIKV RJOX" "FREKEY"
CLEARTEXT
```

### NIVEL 5

Ingresa las credenciales para el Nivel 5:

* **Usuario:** `krypton5`
* **Contraseña:** `CLEARTEXT`

<figure><img src="../../.gitbook/assets/image (445).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (446).png" alt=""><figcaption></figcaption></figure>

Si volvemos a [Dcode.fr](https://www.dcode.fr/vigenere-cipher), introducimos el texto de `found1` y seleccionamos la opción de Descifrado Automático, la herramienta realiza ambos pasos instantáneamente por nosotros.

<figure><img src="../../.gitbook/assets/image (447).png" alt=""><figcaption></figcaption></figure>

Usando el script del anterior nivel obtenemos la contraseña para el siguiente nivel. Ejecutándolo con este comando:

```bash
python3 cifrar.py "BELOS Z" "KEYLENGTH"
RANDOM
```

### NIVEL 6

Ingresa las credenciales para el Nivel 6:

* **Usuario:** `krypton6`
* **Contraseña:** `RANDOM`

<figure><img src="../../.gitbook/assets/image (448).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (449).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (450).png" alt=""><figcaption></figcaption></figure>

Creamos un directorio temporal con el comando:

```bash
mktemp -d
```

Esto nos dará la ruta del directorio, así que accedemos a ella.

```bash
cd /tmp/tmp.RIDVnDtkU7
```

Creamos un enlace simbólico para el archivo `keyfile.dat`:

```bash
ln -s /krypton/krypton6/keyfile.dat
```

Luego, creamos un archivo lleno de caracteres 'A':

<pre class="language-bash"><code class="lang-bash">touch lot_of_a
touch lot_of_a_out
<strong>echo "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA" > lot_of_a
</strong>chmod 777 .
</code></pre>

Verificamos el contenido del directorio:

```bash
ls
```

Esto debería mostrar:

```bash
keyfile.dat lot_of_a lot_of_a_out
```

Ejecutamos el comando de cifrado:

```bash
/krypton/krypton6/encrypt6 ./lot_of_a ./lot_of_a_out
```

El resultado debería ser algo como:

```bash
EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHN
```

Hacemos lo mismo con un archivo lleno de caracteres 'B':

```bash
touch lot_of_b
touch lot_of_b_out
chmod 777 .
echo "BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB" > lot_of_b
ls
```

Esto debería mostrar:

```bash
keyfile.dat lot_of_b lot_of_b_out lot_of_a lot_of_a_out
```

Ejecutamos el cifrado nuevamente:

```bash
/krypton/krypton6/encrypt6 ./lot_of_b ./lot_of_b_out
```

El resultado debería ser algo como:

```bash
EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHN
```

De la información del nivel, lo más importante es lo siguiente:

Normalmente, el byte de clave 'aleatorio' se realiza una operación **XOR** con el texto plano para producir el texto cifrado. Si el flujo de clave aleatorio puede ser replicado en el receptor, entonces un XOR adicional producirá nuevamente el texto plano.

He creado este script para obtener la contraseña del siguiente nivel. Este código descifra un texto cifrado utilizando una técnica basada en el cifrado XOR.&#x20;

1. **Cálculo de Desplazamientos**:
   * Se calcula la diferencia entre dos secuencias: una secuencia de caracteres "A" (que actúa como texto plano) y una secuencia de texto cifrado.
   * Se obtiene una lista de desplazamientos a partir de estas diferencias.
2. **Descifrado del Texto Cifrado**:
   * Utiliza los desplazamientos calculados para descifrar el texto cifrado.
   * Resta cada desplazamiento del valor ASCII del carácter cifrado y ajusta el resultado para asegurarse de que esté en el rango de letras ASCII válidas.
   * Compone el texto descifrado.

```python
import string

# Texto cifrado y longitud del texto cifrado
cipher = "PNUKLYLWRQKGKBE"
cipherlength = len(cipher)

# Calcula los desplazamientos comparando el texto cifrado con una secuencia de 'A'
for i in range(0, cipherlength): 
    chosen = "AAAAAAAAAAAAAAA"
    encoded = "EICTDGYIYZKTHNS"
    print(ord(encoded[i]) - ord(chosen[i]))

# Desplazamientos obtenidos: 4, 8, 2, 19, 3, 6, 24, 8, 24, 25, 10, 19, 7, 13, 18

flag = ""
decode = [4, 8, 2, 19, 3, 6, 24, 8, 24, 25, 10, 19, 7, 13, 18]

# Descifra el texto cifrado usando los desplazamientos
for i in range(0, cipherlength): 
    char = ord(cipher[i]) # Obtiene el carácter cifrado
    result = char - decode[i] # Resta el desplazamiento para descifrar
    if (chr(result) not in string.ascii_letters): 
        result = char - decode[i] + 26 # Ajusta el resultado si no está en el rango de letras ASCII

    flag += chr(result) # Construye el texto descifrado

print(flag)
```

<figure><img src="../../.gitbook/assets/image (427).png" alt=""><figcaption></figcaption></figure>

### NIVEL 7

Ingresa las credenciales para el Nivel 7:

* **Usuario:** `krypton7`
* **Contraseña:** `LFSRISNOTRANDOM`

<figure><img src="../../.gitbook/assets/image (428).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>
