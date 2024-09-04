---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  11/05/2024
---

# SecretJenkins

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (593).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (595).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 8080** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (596).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea un panel de **Login**.

<figure><img src="../../.gitbook/assets/image (597).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente no encontramos nada relevante.

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2:8080/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (598).png" alt=""><figcaption></figcaption></figure>

Podemos probar accediendo a ciertos recursos que nos proporcionan respuestas útiles. Por ejemplo, en la sección `/people` podemos observar tanto la versión en la parte inferior como varias opciones de botones.

<figure><img src="../../.gitbook/assets/image (602).png" alt=""><figcaption></figcaption></figure>

Al hacer clic en el botón **people**, se muestra información sobre dos posibles usuarios.

<figure><img src="../../.gitbook/assets/image (603).png" alt=""><figcaption></figcaption></figure>

Probamos con credenciales genéricas para ambos usuarios, pero no pudimos acceder. Dado que tenemos la versión del sistema, buscamos algún exploit que podamos utilizar. Encontramos varias opciones en Google.

Para esta ocasión, utilizaremos un exploit disponible en [Exploit-DB](https://www.exploit-db.com/exploits/51993). Descargamos el archivo `.py` correspondiente, lo ejecutamos y logramos obtener la información del archivo que estábamos buscando.

```bash
python3 script.py -u http://172.17.0.2:8080 -p /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (604).png" alt=""><figcaption></figcaption></figure>

Sabemos que tenemos la capacidad de leer archivos. En este caso, buscaremos las credenciales de los usuarios de Jenkins leyendo el archivo `/var/jenkins_home/users/users.xml`. Este archivo contiene información sobre los usuarios creados, incluidas las credenciales, permisos y roles asociados. Al ejecutar el comando para leer este archivo, podremos observar el ID de los usuarios.

<figure><img src="../../.gitbook/assets/image (606).png" alt=""><figcaption></figcaption></figure>

Una vez que tenemos el identificador del usuario, podemos aprovecharlo para acceder a su archivo de configuración, donde se encuentra su contraseña en formato hash. El archivo está ubicado en el directorio `/var/jenkins_home/users/admin_4715893701316860439/config.xml.` Al ejecutar el exploit, conseguimos obtener el `passwordHash`.

<figure><img src="../../.gitbook/assets/image (607).png" alt=""><figcaption></figcaption></figure>

Para romper el hash, guardamos el valor en un archivo llamado `hash`. Intentamos obtener la contraseña, pero no tuvimos éxito, ya que no se encuentra en el diccionario utilizado.

Con **john** no se puede por lo que probamos por **SSH** con el usuario **bobby**.

<figure><img src="../../.gitbook/assets/image (610).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l bobby -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**.

<figure><img src="../../.gitbook/assets/image (609).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **russoski**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh bobby@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (611).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (612).png" alt=""><figcaption></figcaption></figure>

Para aprovechar esta situación, visitamos la página de [GTFOBins](https://gtfobins.github.io/gtfobins/python/#sudo) y buscamos la entrada correspondiente a Python. Encontramos un código en la sección de `sudo` que podemos ejecutar, aunque es importante considerar ciertas modificaciones, ya que no es para obtener acceso root directamente.

Antes de ejecutar el comando completo, es necesario agregar la opción `-u pinguinito`, ya que los permisos están asignados a ese usuario. Si ingresamos el comando correctamente, podremos confirmar que hemos cambiado al usuario `pinguinito`.

```bash
sudo -u pinguinito python3 -c 'import os; os.system("/bin/sh")'
```

<figure><img src="../../.gitbook/assets/image (613).png" alt=""><figcaption></figcaption></figure>

Al inspeccionar el script, verificamos que somos los propietarios del archivo, y además, este tiene permisos de lectura y ejecución. Al abrir el archivo, descubrimos que su función principal es copiar `/opt/script.py` al directorio `/tmp` bajo el nombre `script_backup.py`, seguido de un mensaje que confirma la copia exitosa.

Dado que somos los propietarios, intentamos agregar permisos de escritura al script. Este cambio se realizó correctamente, lo que nos brinda la oportunidad de aprovecharlo para realizar modificaciones adicionales.

<figure><img src="../../.gitbook/assets/image (614).png" alt=""><figcaption></figcaption></figure>

```bash
chmod +w /opt/script.py
```

Modificamos el archivo para que podamos tener acceso a una nueva **shell**.

```bash
echo 'import os; os.system("/bin/sh")' > /opt/script.py
```

<figure><img src="../../.gitbook/assets/image (615).png" alt=""><figcaption></figcaption></figure>

Ya tenemos acceso como **root** por lo que tenemos los máximos privilegios.
