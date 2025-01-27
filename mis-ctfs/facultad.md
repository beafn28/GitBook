---
description: >-
  ✍️ Autor: beafn28🔍 Dificultad: Fácil📅 Fecha de creación: 30/1/2025 🖥️The
  Hacker Labs
hidden: true
---

# Facultad

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.61
```

para verificar la conectividad de red.

<figure><img src="../.gitbook/assets/image (1034).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 192.168.1.61
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

<figure><img src="../.gitbook/assets/image (1035).png" alt=""><figcaption></figcaption></figure>

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 192.168.1.61
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../.gitbook/assets/image (1036).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea como el Studium de una asignatura en este caso se llama Administración de Sistemas.

<figure><img src="../.gitbook/assets/image (1037).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://192.168.1.61/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../.gitbook/assets/image (1038).png" alt=""><figcaption></figcaption></figure>

Vamos a ver el contenido del directorio`/images`.

<figure><img src="../.gitbook/assets/image (1039).png" alt=""><figcaption></figcaption></figure>

Nos la descargamos para realizar la técnica de esteganografía.

<figure><img src="../.gitbook/assets/image (1040).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido del archivo. Descubrimos dos posibles usuarios.

<figure><img src="../.gitbook/assets/image (1041).png" alt=""><figcaption></figcaption></figure>

Vamos a ver el contenido del directorio`/education`.&#x20;

<figure><img src="../.gitbook/assets/image (1042).png" alt=""><figcaption></figcaption></figure>

Como podemos comprobar es un WordPress por lo que añadimos el dominio **facultad.thl** a **/etc/hosts**.

<figure><img src="../.gitbook/assets/image (1043).png" alt=""><figcaption></figcaption></figure>

Al tener el directorio wp-admin sabemos que es un panel de login pero no tenemos las credenciales por lo que con **WPScan** enumeramos el usuario.

```bash
wpscan --url http://192.168.1.61/education/ --enumerate u,vp
```

<figure><img src="../.gitbook/assets/image (1044).png" alt=""><figcaption></figcaption></figure>

Sabiendo el usuario tenemos que conseguir la contraseña.

```bash
wpscan --url http://192.168.1.61/education/ --passwords /usr/share/wordlists/rockyou.txt --usernames facultad
```

<figure><img src="../.gitbook/assets/image (1045).png" alt=""><figcaption></figcaption></figure>

Ingresamos las credenciales en el panel de **login**.

<figure><img src="../.gitbook/assets/image (1046).png" alt=""><figcaption></figcaption></figure>

