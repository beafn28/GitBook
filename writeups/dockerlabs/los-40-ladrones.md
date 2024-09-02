---
description: '✍️ Autor: firstatack🔍 Dificultad: Fácil  📅 Fecha de creación: 04/07/2024'
---

# Los 40 ladrones

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el puerto **80** perteneciente al servicio **HTTP** está abierto, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre la página por defecto Apache2.

<figure><img src="../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

De primeras, no se nos muestra nada, por lo que revisamos el código fuente por si hay alguna información oculta relevante pero no vemos nada.

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado un archivo de texto llamado `qdefense.txt`. A continuación, procederemos a visualizar su contenido en la página web.

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

En el archivo `qdefense.txt` encontramos información relevante, como un posible usuario del sistema llamado "toctoc" y una serie de números que parecen corresponder a una secuencia de puertos (7000, 8000, 9000). Esta secuencia sugiere el uso de una técnica conocida como **port knocking**. En esta técnica, un firewall está configurado para abrir un puerto específico solo si se detecta una conexión en una secuencia de puertos predefinida.

Para llevar a cabo este ataque, utilizamos la herramienta `knock`. Dado que `knock` no está incluida por defecto en Kali Linux, primero debemos instalarla. Luego, ejecutamos el siguiente comando en la terminal para enviar la secuencia de puertos:

```bash
knock 172.17.0.2 7000 8000 9000
```

Para verificar si se ha abierto algún puerto adicional tras realizar el port knocking, volvemos a realizar un escaneo de puertos. Utilizamos `nmap` para este propósito. Aquí están los comandos para hacerlo:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

Este comando escaneará todos los puertos de la dirección IP `172.17.0.2` para identificar cualquier puerto adicional que se haya abierto.

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

Ahora que el puerto **SSH** (puerto 22) está abierto, podemos intentar obtener la contraseña del usuario **toctoc** utilizando un ataque de fuerza bruta con `Hydra`.

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l toctoc -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -f
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**.&#x20;

<figure><img src="../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto el **usuario** de esa contraseña. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh toctoc@172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado dos archivos que podríamos utilizar para escalar privilegios y obtener acceso como usuario **root**. De estos, nos centraremos en `/opt/bash`, ya que parece simular el intérprete de comandos de Linux utilizado para interpretar el código introducido por el usuario en la línea de comandos.

Para ejecutarlo, escribimos el siguiente comando en la terminal:

```bash
sudo /opt/bash
```

<figure><img src="../../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

Finalmente, hemos logrado obtener acceso como usuario **root**, lo que nos concede todos los privilegios disponibles en el sistema.
