---
description: '✍️ Autor: Trr0r🔍 Dificultad: Fácil 📅 Fecha de creación: 22/04/2025'
---

# Bicho (Por completar)

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/imagen (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/imagen (2).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP** está abierto por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/imagen (4).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80 y se nos redirige al dominio **bicho.dl** por lo que añadimos al directorio `/etc/hosts`.

<figure><img src="../../.gitbook/assets/imagen (3).png" alt=""><figcaption></figcaption></figure>

Tras añadirlo volvemos a recargar la página y nos encontramos como en estos casos un WordPress.

<figure><img src="../../.gitbook/assets/imagen (5).png" alt=""><figcaption></figcaption></figure>

Revisando los post encontramos que el autor se llama Bicho por lo que lo tendremos en cuenta más adelante.&#x20;

### 🚀 **EXPLOTACIÓN**

Al tener el directorio wp-admin sabemos que es un panel de login pero no tenemos las credenciales por lo que con **WPScan** enumeramos el usuario.

```bash
wpscan --url http://bicho.dl/ --enumerate u,vp
```

<figure><img src="../../.gitbook/assets/imagen.png" alt=""><figcaption></figcaption></figure>
