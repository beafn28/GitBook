---
description: '✍️ Autor: Hack_Viper🔍 Dificultad: Fácil 📅 Fecha de creación: 12/01/2025'
---

# Extraviado

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/imagen (85).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/imagen (86).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/imagen (87).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Como vemos hay una cadena en Base64 por lo que vamos a decodificar.&#x20;

<figure><img src="../../.gitbook/assets/imagen (88).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/imagen (89).png" alt=""><figcaption></figcaption></figure>

Al parecer son las credenciales del usuario por lo que iniciamos sesión.

<figure><img src="../../.gitbook/assets/imagen (90).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar. No tenemos permisos **SUDO**.

<figure><img src="../../.gitbook/assets/imagen (91).png" alt=""><figcaption></figcaption></figure>

Indagamos entre los directorios y nos damos cuenta que hay otro usuario llamado **diego** y tenemos la contraseña en base64.

<figure><img src="../../.gitbook/assets/imagen (92).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/imagen (93).png" alt=""><figcaption></figcaption></figure>

Iniciamos sesión con esas credenciales e indagando entre los directorios un acertijo.

<figure><img src="../../.gitbook/assets/imagen (94).png" alt=""><figcaption></figcaption></figure>

El animal sobre que nos habla el acertijo es el **osoazul** que es la contraseña de **root**.

<figure><img src="../../.gitbook/assets/imagen (95).png" alt=""><figcaption></figcaption></figure>
