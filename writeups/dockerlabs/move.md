---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  30/06/2024
---

# Move

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (497).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (498).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** y el **puerto 3000** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,3000 -v 172.17.0.2
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (499).png" alt=""><figcaption></figcaption></figure>

El puerto **80** y el **3000** podemos encontrar servicios web. Es curioso que en el 3000 nos aparece una redirección a un panel de **Login**. Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre la página por defecto **Apache2**.

<figure><img src="../../.gitbook/assets/image (500).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente por si hay algo relevante tampoco hay información relevante pero no había nada.

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (502).png" alt=""><figcaption></figcaption></figure>

Encontramos un archivo que nos llama la atención que se llama `/maintenance.html` así que accederemos a él por si hay algo relevante.

<figure><img src="../../.gitbook/assets/image (501).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado un encabezado en **HTML** que revela la ruta de un archivo potencialmente interesante, aunque actualmente no tenemos acceso al sistema para explorarlo más a fondo. Guardaremos esta información para usarla en el futuro.

Dado que no encontramos nada más relevante en este momento, procederemos a realizar la enumeración del **puerto 3000** en la máquina objetivo.

<figure><img src="../../.gitbook/assets/image (503).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado un panel de inicio de sesión de **Grafana**. Lo más notable es que en la parte inferior del panel se muestra la **versión** específica del servicio. Investigaremos esta versión para determinar si existen vulnerabilidades conocidas que puedan facilitarnos el acceso inicial al sistema.

<figure><img src="../../.gitbook/assets/image (504).png" alt=""><figcaption></figcaption></figure>

Hemos confirmado que la versión específica de **Grafana** que encontramos es vulnerable a un **exploit** que permite la lectura de archivos en el sistema. Procederemos a descargar este exploit en nuestra máquina atacante y comenzaremos el proceso de intrusión.

### 🚀 **EXPLOTACIÓN**

Con el **exploit** descargado y preparado en nuestra máquina atacante, procederemos a ejecutarlo para aprovechar la vulnerabilidad detectada en esta versión de **Grafana**.

<figure><img src="../../.gitbook/assets/image (505).png" alt=""><figcaption></figcaption></figure>

Hemos logrado explotar la vulnerabilidad de manera exitosa, lo que nos permite leer cualquier archivo en el sistema que no requiera privilegios elevados. Por lo que leemos el archivo **/tmp/pass.txt**.

<figure><img src="../../.gitbook/assets/image (506).png" alt=""><figcaption></figcaption></figure>

Teniendo en cuenta que el **puerto 22** está abierto y que hay un servicio **SSH** activo, procedemos a enumerar los usuarios presentes en el sistema, así como a inspeccionar el archivo mencionado en el **puerto 80**. Accederemos al usuario **freddy**.

<figure><img src="../../.gitbook/assets/image (507).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (508).png" alt=""><figcaption></figcaption></figure>

Como podemos ver, el usuario tiene permisos para ejecutar un comando `sudo` asociado a un script sobre el cual tiene permisos de escritura. Esto nos permite realizar una escalada de privilegios modificando el script para que ejecute las acciones que deseemos. Al ser ejecutado como **root** mediante `sudo`, el script no enfrentará restricciones de privilegios. Con esto en mente, podríamos alterar el script para que inicie una **shell** interactiva con privilegios de **root**. En mi caso, opté por darle permisos **SUID** al binario de **bash**, lo que nos permitirá obtener una **shell** como **root** en cualquier momento, asegurando una alta persistencia en el sistema.

<figure><img src="../../.gitbook/assets/image (509).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (510).png" alt=""><figcaption></figcaption></figure>

Hemos logrado modificar los permisos del binario `bash`. Ahora, solo queda utilizar el comando `bash -p` para iniciar una **shell** con privilegios de **root**.

<figure><img src="../../.gitbook/assets/image (577).png" alt=""><figcaption></figcaption></figure>
