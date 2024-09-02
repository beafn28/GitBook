---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  29/07/2024
---

# ConsoleLog

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **80** perteneciente al servicio **HTTP**, el puerto **3000** perteneciente al servicio **PPP** y el puerto **5000** perteneciente al servicio **UPnP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,3000,5000 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (333).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web aparezca bienvenida.

<figure><img src="../../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>

De primeras, no se nos muestra nada, por lo que revisamos el código fuente por si hay alguna información oculta relevante.

<figure><img src="../../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>

Después de revisar el código, notamos que se hace referencia a un archivo llamado `authentication.js`. Al examinar este archivo, encontramos el siguiente contenido:

<figure><img src="../../.gitbook/assets/image (336).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:&#x20;

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>

Al acceder al directorio `/backend`, encontramos que hay varios archivos disponibles.

<figure><img src="../../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

Dentro de los archivos, hay uno llamado `server.js`. Este archivo parece configurar un servidor que escucha peticiones POST en la ruta `/recurso/` y devuelve una contraseña si el token proporcionado es válido.

<figure><img src="../../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>

Podemos verificar esto utilizando `curl`. Al enviar una petición POST a esta ruta, podemos ver la contraseña si el token proporcionado es correcto; de lo contrario, se recibirá un mensaje diferente. Usamos el siguiente comando para hacer la prueba:

```bash
curl -X POST http://172.17.0.2:3000/recurso/ -H "Content-Type: application/json" -d '{"token": "tokentraviesito"}'
```

<figure><img src="../../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -L /opt/SecLists/Usernames/xato-net-10-million-usernames.txt -p lapassworddebackupmaschingonadetodas ssh://172.17.0.2:5000 -t 64
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2** para obtener el usuario con esa contraseña.

<figure><img src="../../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>

Al realizar el ataque de fuerza bruta, hemos descubierto el **usuario** de esa contraseña. Sabiendo esto, nos conectamos a través de **SSH** al usuario con el comando:

```bash
ssh lovely@172.17.0.2 -p 5000
```

<figure><img src="../../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar.

<figure><img src="../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

Nos dirigimos al directorio raíz y buscamos archivos con permisos SUID. Encontramos que el archivo `nano` tiene estos permisos.

```bash
cd /
find \-perm -4000 -user root 2>/dev/null
```

Revisando podemos observar que podemos abusar de este permiso [SUID](https://gtfobins.github.io/gtfobins/nano/#limited-suid) pero es limitado.

Al revisar los permisos **SUID**, notamos que aunque podemos explotar el permiso de `nano`, está limitado en su funcionalidad. Por lo tanto, investigamos si solo hay el usuario `lovely` o si existen otros usuarios. Al examinar el archivo `/etc/passwd`, descubrimos que hay un usuario adicional.

<figure><img src="../../.gitbook/assets/image (348).png" alt=""><figcaption></figcaption></figure>

Antes de seguir explorando otras formas de escalar privilegios, recordemos que tenemos la capacidad de ejecutar comandos como `root` usando `nano`. Esto nos permite editar el archivo `/etc/passwd` y eliminar la `x` para permitir el acceso sin necesidad de credenciales.

<figure><img src="../../.gitbook/assets/image (350).png" alt=""><figcaption></figcaption></figure>

Después de realizar esta modificación, podremos acceder como `root` sin necesidad de credenciales. Con esto, hemos completado el acceso a la máquina.

![](<../../.gitbook/assets/image (349).png>)\
