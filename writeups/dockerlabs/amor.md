---
description: '✍️ Autor: Romabri🔍 Dificultad: Fácil  📅 Fecha de creación: 26/04/2024'
---

# Amor

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/0 (5).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (5).png>)

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/2 (5).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea de una empresa de seguridad.

![](<../../.gitbook/assets/3 (5).png>)

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l carlota -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**. Hemos probado con **Juan**, pero el usuario que responde es **carlota**.

Al realizar el ataque de fuerza bruta, descubrimos la contraseña de **carlota**, por lo que nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh carlota@172.17.0.2
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

para ver si hay algo para explotar.

![](<../../.gitbook/assets/5 (5).png>)

Indagando entre los directorios encontramos que hay una imagen por lo que a través de **SFTP** la trasladaremos a nuestra propia máquina. Esto se puede llevar a cabo ya que conocemos las credenciales perfectamente.

![](<../../.gitbook/assets/6 (4).png>)

![](<../../.gitbook/assets/7 (4).png>)

Con el comando:

```bash
steghide extract -sf imagen.jpg
```

extraemos datos ocultos de un archivo de imagen llamado **imagen.jpg** usando la herramienta **Steghide**, obteniendo un archivo llamado **secret.txt**.

![](<../../.gitbook/assets/8 (4).png>)

![](<../../.gitbook/assets/9 (4).png>)

Como comprobamos que el contenido está en **base64**, lo decodificamos con el comando:

```bash
echo "ZXNsYWNhc2FkZXBpbnlwb24=" | base64 --decode
```

![](<../../.gitbook/assets/10 (3).png>)

Al parecer, es una contraseña. En la carpeta **home** hay tres usuarios en los cuales podemos probar esa contraseña. Nos damos cuenta de que podemos acceder como **Óscar**. Al tener acceso, observamos los permisos y explotamos el binario de **Ruby** con el comando:

```bash
sudo ruby -e 'exec "/bin/sh"
```

<figure><img src="../../.gitbook/assets/image (525).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (526).png" alt=""><figcaption></figcaption></figure>
