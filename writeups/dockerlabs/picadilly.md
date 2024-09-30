---
description: '✍️ Autor: kaikoperez🔍 Dificultad: Fácil  📅 Fecha de creación: 18/05/2024'
---

# Picadilly

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (537).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **443** perteneciente al servicio **HTTPS** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Para saber más información sobre el puerto abierto, se realiza el siguiente comando:

```bash
nmap -sCV -p80,443 172.17.0.2 -oN targeted
```

<figure><img src="../../.gitbook/assets/image (538).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web aparezca con un archivo y miramos lo que contiene.

<figure><img src="../../.gitbook/assets/image (539).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (540).png" alt=""><figcaption></figcaption></figure>

Tenemos un posible usuario del sistema llamado **mateo**, junto con su contraseña encriptada. Aunque no se especifica el método de encriptación utilizado, se nos ofrece una pista que sugiere que se trata de un **Cifrado César**. Esto nos permite descifrar la contraseña. [Dcode - Cifrado César](https://www.dcode.fr/cifrado-cesar)

<figure><img src="../../.gitbook/assets/image (541).png" alt=""><figcaption></figcaption></figure>

Accedemos al navegador y visitamos la dirección IP `https://172.17.0.2`.

<figure><img src="../../.gitbook/assets/image (542).png" alt=""><figcaption></figcaption></figure>

Se nos presenta una página donde es posible subir archivos como publicaciones que serán visibles en la web. Con esta información, podríamos llevar a cabo un ataque **LFI** (Inclusión de Archivos Locales), subiendo un archivo **PHP** que contenga el código necesario para ejecutar una **reverse shell**.

### 🚀 **EXPLOTACIÓN**

Subimos el archivo de [php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell) de GitHub y nos ponemos en escucha utilizando **Netcat**.

<figure><img src="../../.gitbook/assets/image (543).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (544).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (545).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (546).png" alt=""><figcaption></figcaption></figure>

Logramos acceder al archivo que subimos al servidor, lo que nos permitió recibir una conexión en el puerto de escucha. Con este acceso inicial al sistema, es hora de buscar formas de escalar privilegios para comprometer completamente la máquina y obtener los máximos privilegios.

### 🔐 **PRIVILEGIOS**

Nos conectamos al usuario mateo con la contraseña obtenida anteriormente aunque hay que hacer un ajuste.

<figure><img src="../../.gitbook/assets/image (547).png" alt=""><figcaption></figcaption></figure>

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../../.gitbook/assets/image (548).png" alt=""><figcaption></figcaption></figure>

El usuario tiene permiso para ejecutar el binario `/usr/bin/php` como root sin requerir una contraseña en la línea de comandos. Buscamos información sobre este binario en [GTFOBins](https://gtfobins.github.io/).

<figure><img src="../../.gitbook/assets/image (549).png" alt=""><figcaption></figcaption></figure>

Hemos conseguido tener los máximos privilegios al ser **root**.
