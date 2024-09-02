# Nivel 21-30

### NIVEL 21

Ingresamos las credenciales para el nivel 21.

* **Usuario:** `bandit21`
* **Contraseña:** `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

<figure><img src="../../../.gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

Un programa se está ejecutando automáticamente a intervalos regulares desde cron, el programador de trabajos basado en el tiempo. Busca en `/etc/cron.d/` la configuración y verifica qué comando se está ejecutando.

<figure><img src="../../../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

### NIVEL 22

Ingresamos las credenciales para el nivel 22.

* **Usuario:** `bandit22`
* **Contraseña:** `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

<figure><img src="../../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

Un programa se está ejecutando automáticamente a intervalos regulares desde cron, el programador de tareas basado en el tiempo. Mira en `/etc/cron.d/` para ver la configuración y qué comando está siendo ejecutado.

<figure><img src="../../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

### NIVEL 23

Ingresamos las credenciales para el nivel 23.

* **Usuario:** `bandit23`
* **Contraseña:** `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

<figure><img src="../../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

Un programa se está ejecutando automáticamente a intervalos regulares desde `cron`, el programador de tareas basado en el tiempo. Revisa la configuración en `/etc/cron.d/` para ver qué comando se está ejecutando.

> **NOTA:** Este nivel requiere que crees tu propio script en shell. ¡Este es un gran paso y deberías sentirte orgulloso de ti mismo cuando completes este nivel!
>
> **NOTA 2:** Ten en cuenta que tu script en shell será eliminado una vez que se ejecute, por lo que puede ser una buena idea mantener una copia guardada.

<figure><img src="../../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

```bash
mkdir /tmp/bandit24
cd /tmp/bandit24
ls
echo "cat /etc/bandit_pass/bandit24 > /tmp/bandit24/pass" > script.sh
ls
touch pass
chmod +rwx pass
chmod +rx script.sh
ls
chmod 777 /tmp/bandit24
ls
cp script.sh /var/spool/bandit24/foo/
cat pass
```

La contraseña es `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

### NIVEL 24

Ingresamos las credenciales para el nivel 24.

* **Usuario:** `bandit24`
* **Contraseña:** `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

<figure><img src="../../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

El escenario que describes implica que un programa o "daemon" está escuchando en el puerto 30002, y te proporcionará la contraseña para "bandit25" si le proporcionas la contraseña correcta para "bandit24" y un código PIN secreto de 4 dígitos. Como no se conoce el PIN, la única manera de encontrarlo es mediante un ataque de fuerza bruta, probando todas las posibles combinaciones desde `0000` hasta `9999`. Por lo que he creado este script de bash.

Aquí tienes el script en formato Markdown con el código en Bash:

```bash
#!/bin/bash

for i in {0000..9999}
do
        echo gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i >> possibilities.txt
done

cat possibilities.txt | nc localhost 30002 > result.txt
```

Este script genera todas las combinaciones posibles de 4 dígitos y las guarda en un archivo `possibilities.txt` junto con la contraseña `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`. Luego, usa `nc` para enviar el contenido del archivo al puerto `30002` del localhost, y guarda la respuesta en `result.txt`.

<figure><img src="../../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

La contraseña es `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

### NIVEL 25

Ingresamos las credenciales para el nivel 25.

* **Usuario:** `bandit25`
* **Contraseña:** `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

<figure><img src="../../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

El problema consiste en iniciar sesión en el usuario `bandit26` desde `bandit25`. Sin embargo, la shell del usuario `bandit26` no es `/bin/bash`, sino otra diferente. El objetivo es descubrir qué shell es, cómo funciona y cómo salir de ella para obtener un acceso completo a la terminal.

El script utiliza el comando `more` para mostrar un arte ASCII y luego sale directamente. Por lo tanto, el comando `more` podría ser un punto de entrada que podríamos explotar para obtener una shell normal. Ahora, necesitamos redimensionar nuestra terminal para activar los efectos del comando `more` y entrar en el modo de visualización de comandos.

Para editar un archivo que se está viendo con `more`, presiona `v`. Esto lanzará el editor de texto que está configurado en la variable de entorno `$EDITOR` en la línea que se está viendo.

Introduce `:e /etc/bandit_pass/bandit26` y entra en el modo de edición para verificar la contraseña de `bandit26`.

<figure><img src="../../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

### NIVEL 26

Ingresamos las credenciales para el nivel 26.

* **Usuario:** `bandit26`
* **Contraseña:** `s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

<figure><img src="../../../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

### NIVEL 27

Ingresamos las credenciales para el nivel 27.

* **Usuario:** `bandit27`
* **Contraseña:** `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`

<figure><img src="../../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

Hay un repositorio de Git en `ssh://bandit27-git@localhost/home/bandit27-git/repo` a través del puerto 2220. La contraseña para el usuario `bandit27-git` es la misma que la de `bandit27`.

```bash
# Clonar el repositorio de Git
git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
```

Salida:

```less
lessCopiar códigoCloning into 'repo'...
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit27/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit27/.ssh/known_hosts).
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit27-git@localhost's password:
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

```bash
# Listar el contenido del directorio 'repo'
ls -al repo
```

Salida:

```yaml
total 16
drwxrwxr-x 3 bandit27 bandit27 4096 Aug 23 19:10 .
drwxrwxr-x 3 bandit27 bandit27 4096 Aug 23 19:10 ..
drwxrwxr-x 8 bandit27 bandit27 4096 Aug 23 19:10 .git
-rw-rw-r-- 1 bandit27 bandit27   68 Aug 23 19:10 README
```

```bash
cat repo/README
```

Salida:

```vbnet
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

### NIVEL 28

Ingresamos las credenciales para el nivel 28.

* **Usuario:** `bandit28`
* **Contraseña:** `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`

<figure><img src="../../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

### NIVEL 29

Ingresamos las credenciales para el nivel 29.

* **Usuario:** `bandit29`
* **Contraseña:** `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

<figure><img src="../../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

### NIVEL 30

Ingresamos las credenciales para el nivel 30.

* **Usuario:** `bandit30`
* **Contraseña:** `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

<figure><img src="../../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>
