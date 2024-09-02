---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  05/07/2024
---

# NodeClimb

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (493).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (494).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP** y el **puerto 22** perteneciente al **servicio SSH**  están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,22 -v 172.17.0.2
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (495).png" alt=""><figcaption></figcaption></figure>

Podemos observar que en el **puerto 21** se encuentra un **secretitopicaron.zip** almacenado y, dado que se permite el inicio de sesión anónimo, procederemos a conectarnos para descargar este archivo y acceder a su contenido.

<figure><img src="../../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

El archivo está cifrado y necesitamos una clave para descifrarlo. Utilizamos la herramienta `zip2john` de Kali Linux para obtener el hash del archivo. Posteriormente, desciframos este hash con `John the Ripper`.

<figure><img src="../../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

Hemos conseguido la contraseña necesaria para extraer el contenido del archivo ZIP, la cual es `"password1"`. Ahora, al ejecutar el comando `unzip` en la CLI.

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

Parece que tenemos las credenciales del usuario **mario** en la máquina vulnerable. Las utilizaremos para establecer una conexión **SSH** con ella.

<figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (113).png" alt=""><figcaption></figcaption></figure>

Para verificar los archivos binarios que el usuario `mario` puede ejecutar, observamos que tiene permisos para ejecutar el binario `/usr/bin/node` y un script **JavaScript** ubicado en la ruta `/home/mario/script.js` con privilegios elevados. Esto podría permitirnos realizar una escalada de privilegios y obtener acceso como usuario **root**.

El archivo se encuentra vacío por lo que añadimos la siguiente línea de código.

```javascript
require('child_process').spawn('/bin/sh', { stdio: [0, 1, 2] });
```

Ejecutamos el siguiente comando en la terminal para llevar a cabo la escalada de privilegios.

```bash
sudo /usr/bin/node /home/mario/script.js
```

<figure><img src="../../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>

Hemos conseguido acceso como **root**, lo que nos otorga todos los permisos en el sistema.
