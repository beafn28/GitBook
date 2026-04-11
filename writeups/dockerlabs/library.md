---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  13/05/2024
---

# Library

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (478).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (479).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (480).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre la página por defecto Apache2.

<figure><img src="../../.gitbook/assets/image (481).png" alt=""><figcaption></figcaption></figure>

De primeras, no se nos muestra nada, por lo que revisamos el código fuente por si hay alguna información oculta relevante pero no vemos nada.

<figure><img src="../../.gitbook/assets/image (482).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (484).png" alt=""><figcaption></figcaption></figure>

Hemos identificado un archivo `index.php` en el sistema. Vamos a examinar su contenido para obtener más información.

<figure><img src="../../.gitbook/assets/image (483).png" alt=""><figcaption></figcaption></figure>

Al revisar el archivo `index.php`, encontramos una cadena que parece ser una posible contraseña. Dado que el puerto **22** (SSH) está abierto, y no hemos encontrado otros elementos de interés, procederemos a realizar un ataque de fuerza bruta para intentar encontrar un usuario que esté utilizando esta contraseña para acceder por **SSH**.

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -L /opt/SecLists/Usernames/xato-net-10-million-usernames.txt -p JIFGHDS87GYDFIGD ssh://172.17.0.2 -t 64
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2** para obtener el usuario con esa contraseña.

<figure><img src="../../.gitbook/assets/image (486).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto el **usuario** de esa contraseña. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh carlos@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (487).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (488).png" alt=""><figcaption></figcaption></figure>

Carlos tiene permisos para ejecutar un script con privilegios de **root** utilizando el comando `sudo`. Vamos a investigar cómo funciona este script con el objetivo de identificar una posible manera de escalar nuestros privilegios en el sistema.

<figure><img src="../../.gitbook/assets/image (489).png" alt=""><figcaption></figcaption></figure>

Contamos con un script que se encarga de copiar archivos a una ruta específica. Lo interesante es que este script depende de una librería particular. Dado que la máquina se llama _Library_, podemos inferir que es posible llevar a cabo un ataque de _**Library Hijacking**_. Esto implica que, si logramos crear una librería maliciosa, podríamos hacer que el script ejecute nuestras órdenes al intentar acceder a ella.

Además, como tenemos la capacidad de ejecutar el script con privilegios de **root**, podemos utilizar esta técnica para realizar cualquier cambio en el sistema. En este caso, emplearemos el ataque para otorgar permisos **SUID** al binario `bash`, lo que nos permitirá escalar privilegios de manera efectiva.

<figure><img src="../../.gitbook/assets/image (490).png" alt=""><figcaption></figcaption></figure>

Hemos identificado que tenemos permisos de escritura en el directorio `/opt`. Aprovecharemos esta situación creando una librería maliciosa con el mismo nombre que la librería requerida por el script. Dentro de esta librería, incluiremos las instrucciones que deseamos ejecutar.

<figure><img src="../../.gitbook/assets/image (495).png" alt=""><figcaption></figcaption></figure>

Con todo listo, simplemente necesitamos ejecutar el script con `sudo`. Cuando el script intente acceder a la librería, buscará primero en el directorio actual, lo que activará nuestra carga maliciosa.

<figure><img src="../../.gitbook/assets/image (494).png" alt=""><figcaption></figcaption></figure>

Hemos logrado modificar los permisos del binario `bash`. Ahora, solo queda utilizar el comando `bash -p` para iniciar una **shell** con privilegios de **root**.

<figure><img src="../../.gitbook/assets/image (27) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
