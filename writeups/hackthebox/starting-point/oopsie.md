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
