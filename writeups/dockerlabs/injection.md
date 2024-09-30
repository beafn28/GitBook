---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Muy Fácil  📅 Fecha de creación:
  09/04/2024
---

# Injection

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/0 (8).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (9).png>)

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Utilizamos el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información específica sobre esos puertos.

<figure><img src="../../.gitbook/assets/2 (9).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagaremos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea un formulario de iniciar sesión.

![](<../../.gitbook/assets/3 (9).png>)

Se intenta hacer **Login** con las credenciales más típicas como **admin:admin** y **admin:password** pero no hay suerte y si indagamos a través del código fuente tampoco hay nada que llamé la atención.

### 🚀 **EXPLOTACIÓN**

El nombre de la máquina nos sugiere que podría ser vulnerable a una **SQL Injection**, así que vamos a intentar hacer un **bypass**. Para probar, añadimos una comilla simple con una contraseña cualquiera.

![](<../../.gitbook/assets/4 (9).png>)

Ingresamos en el campo de usuario:

```sql
admin' or 1=1-- -
```

para que la condición sea siempre verdadera y en el campo de contraseña, ponemos cualquier valor.

![](<../../.gitbook/assets/5 (8).png>)

Como podemos observar nos hemos logueado correctamente y hemos descubierto información relevante como el nombre del usuario y la contraseña. Sabiendo esto nos conectamos a través de **SSH** con esas credenciales.

![](<../../.gitbook/assets/6 (7).png>)

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que ejecutamos:

```bash
sudo -l
```

para ver si hay algo que podamos explotar.

![](<../../.gitbook/assets/7 (7).png>)

Vemos que **sudo** no es funcional, así que decidimos probar otra vía, utilizando **SUID**. Exploramos los binarios con permisos **SUID** en el directorio raíz usando el comando:

```bash
find / -perm -4000 2>/dev/null
```

![](<../../.gitbook/assets/8 (7).png>)

Para saber que binarios se pueden ejecutar los buscamos en este enlace [Github](https://gtfobins.github.io/).&#x20;

Usamos la explotación **SUID** con **env** a través del comando:

```bash
/usr/bin/env /bin/sh -p
```

Finalmente, hemos conseguido ser **root**.

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
