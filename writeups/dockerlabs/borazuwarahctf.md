---
description: >-
  ✍️ Autor: BorazuwarahCTF 🔍 Dificultad: Muy Fácil  📅 Fecha de creación:
  28/05/2024
---

# BorazuwarahCTF

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/0 (7).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (8).png>)

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Ingresamos la dirección IP en el navegador, lo que nos llevó a una página web que muestra una imagen de un **huevo kinder**. Decidimos descargar la imagen en caso de que contenga alguna información oculta relevante.

<figure><img src="../../.gitbook/assets/2 (8).png" alt=""><figcaption></figcaption></figure>

Utilizamos el comando:

```bash
steghide extract -sf imagen.jpeg
```

para extraer datos ocultos de la imagen usando la herramienta **Steghide**. Tras ejecutarlo, se extrae un archivo llamado **secreto.txt**, pero no contiene nada relevante.



<figure><img src="../../.gitbook/assets/3 (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/4 (8).png" alt=""><figcaption></figcaption></figure>

Aunque no tenemos nada relevante, decidimos seguir indagando en la imagen. Con el comando:

```bash
exiftool imagen.jpeg
```

extraemos y mostramos los metadatos incrustados en la imagen. Nos damos cuenta de que nos indica el usuario, pero no la contraseña, ya que está vacía.

![](<../../.gitbook/assets/5 (7).png>)

### 🚀  **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

utilizando la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de la máquina con la IP **172.17.0.2**.

![](<../../.gitbook/assets/6 (6).png>)

Al realizar el ataque de fuerza bruta, descubrimos la contraseña del **usuario**. Con esta información, nos conectamos a través de **SSH** con el comando:

```bash
ssh borazuwarah@172.17.0.2
```

![](<../../.gitbook/assets/7 (6).png>)

### 🔐 **PRIVILEGIOS**

Se nos indica que el usuario tiene permisos **sudo** para ejecutar cualquier comando en el sistema y, específicamente, puede ejecutar **/bin/bash** sin necesidad de ingresar una contraseña.

El comando:

```bash
sudo bash
```

permitió que el usuario **borazuwarah** obtuviera una sesión de shell con privilegios de **root**, como se verifica al ejecutar:

```bash
whoami
```

y recibir la respuesta **root**. Esto confirma que **borazuwarah** tiene acceso completo al sistema con permisos de administrador.

<figure><img src="../../.gitbook/assets/image (574).png" alt=""><figcaption></figcaption></figure>
