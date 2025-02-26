---
description: '✍️ Autor: JuanR🔍 Dificultad: Fácil 📅 Fecha de creación: 13/02/2025'
---

# Walking Dead

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1330).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1331).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 210100 (1).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 210527.png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente encontramos una carpeta llamado **hidden** con un archivo **shell.php**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-26 210747.png" alt=""><figcaption></figcaption></figure>

Revisamos directorios.

```
```
