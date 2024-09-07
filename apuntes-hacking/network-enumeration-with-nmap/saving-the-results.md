# Saving the Results

## Diferentes Formatos

Cuando ejecutamos varios tipos de escaneos, siempre es recomendable guardar los resultados. Estos pueden ser utilizados posteriormente para examinar las diferencias entre los métodos de escaneo que hemos empleado. Nmap puede guardar los resultados en 3 formatos diferentes:

* **Salida normal (-oN)** con la extensión de archivo `.nmap`
* **Salida grepable (-oG)** con la extensión de archivo `.gnmap`
* **Salida en XML (-oX)** con la extensión de archivo `.xml`

También podemos especificar la opción (-oA) para guardar los resultados en todos los formatos. El comando podría verse de la siguiente manera:

#### Guardando los Resultados

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.28 -p- -oA target
```

* **10.129.2.28**: Escanea el objetivo especificado.
* **-p-**: Escanea todos los puertos.
* **-oA target**: Guarda los resultados en todos los formatos, comenzando el nombre de cada archivo con 'target'. Si no se proporciona una ruta completa, los resultados se almacenarán en el directorio en el que estamos actualmente.

#### Ejemplo de Salida

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 12:14 CEST
Nmap scan report for 10.129.2.28
Host is up (0.0091s latency).
Not shown: 65525 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
25/tcp    open  smtp
80/tcp    open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 10.22 seconds
```

Después, podemos observar los diferentes formatos que Nmap ha creado:

```bash
sherlock28@htb[/htb]$ ls
target.gnmap target.xml  target.nmap
```

### Salida Normal

```bash
sherlock28@htb[/htb]$ cat target.nmap
```

```bash
bashCopiar código# Nmap 7.80 scan initiated Tue Jun 16 12:14:53 2020 as: nmap -p- -oA target 10.129.2.28
Nmap scan report for 10.129.2.28
Host is up (0.053s latency).
Not shown: 4 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
25/tcp open  smtp
80/tcp open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

# Nmap done at Tue Jun 16 12:15:03 2020 -- 1 IP address (1 host up) scanned in 10.22 seconds
```

### Salida Grepable

```bash
sherlock28@htb[/htb]$ cat target.gnmap
```

```bash
# Nmap 7.80 scan initiated Tue Jun 16 12:14:53 2020 as: nmap -p- -oA target 10.129.2.28
Host: 10.129.2.28 ()	Status: Up
Host: 10.129.2.28 ()	Ports: 22/open/tcp//ssh///, 25/open/tcp//smtp///, 80/open/tcp//http///	Ignored State: closed (4)
# Nmap done at Tue Jun 16 12:14:53 2020 -- 1 IP address (1 host up) scanned in 10.22 seconds
```

### Salida en XML

```bash
sherlock28@htb[/htb]$ cat target.xml
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE nmaprun>
<?xml-stylesheet href="file:///usr/local/bin/../share/nmap/nmap.xsl" type="text/xsl"?>
<!-- Nmap 7.80 scan initiated Tue Jun 16 12:14:53 2020 as: nmap -p- -oA target 10.129.2.28 -->
<nmaprun scanner="nmap" args="nmap -p- -oA target 10.129.2.28" start="12145301719" startstr="Tue Jun 16 12:15:03 2020" version="7.80" xmloutputversion="1.04">
<scaninfo type="syn" protocol="tcp" numservices="65535" services="1-65535"/>
<verbose level="0"/>
<debugging level="0"/>
<host starttime="12145301719" endtime="12150323493">
  <status state="up" reason="arp-response" reason_ttl="0"/>
  <address addr="10.129.2.28" addrtype="ipv4"/>
  <address addr="DE:AD:00:00:BE:EF" addrtype="mac" vendor="Intel Corporate"/>
  <hostnames></hostnames>
  <ports>
    <extraports state="closed" count="4">
      <extrareasons reason="resets" count="4"/>
    </extraports>
    <port protocol="tcp" portid="22">
      <state state="open" reason="syn-ack" reason_ttl="64"/>
      <service name="ssh" method="table" conf="3"/>
    </port>
    <port protocol="tcp" portid="25">
      <state state="open" reason="syn-ack" reason_ttl="64"/>
      <service name="smtp" method="table" conf="3"/>
    </port>
    <port protocol="tcp" portid="80">
      <state state="open" reason="syn-ack" reason_ttl="64"/>
      <service name="http" method="table" conf="3"/>
    </port>
  </ports>
  <times srtt="52614" rttvar="75640" to="355174"/>
</host>
<runstats>
  <finished time="12150323493" timestr="Tue Jun 16 12:14:53 2020" elapsed="10.22" summary="Nmap done at Tue Jun 16 12:15:03 2020; 1 IP address (1 host up) scanned in 10.22 seconds" exit="success"/>
  <hosts up="1" down="0" total="1"/>
</runstats>
</nmaprun>
```

## Hojas de Estilo

Con la salida en XML, podemos crear fácilmente informes en formato HTML que son fáciles de leer, incluso para personas no técnicas. Esto es muy útil para la documentación, ya que presenta nuestros resultados de manera detallada y clara.

Para convertir los resultados almacenados en formato XML a HTML, podemos utilizar la herramienta `xsltproc`.

```bash
sherlock28@htb[/htb]$ xsltproc target.xml -o target.html
```

Si ahora abrimos el archivo HTML en nuestro navegador, veremos una presentación clara y estructurada de nuestros resultados.
