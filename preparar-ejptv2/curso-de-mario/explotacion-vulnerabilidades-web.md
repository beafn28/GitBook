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

> Nota: también hay que añadir lo que encontremos

