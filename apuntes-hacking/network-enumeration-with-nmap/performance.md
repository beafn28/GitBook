# Performance

El rendimiento del escaneo juega un papel crucial cuando necesitamos escanear una red extensa o cuando enfrentamos un ancho de banda bajo. Podemos utilizar varias opciones en Nmap para ajustar la velocidad del escaneo, la frecuencia, los tiempos de espera, la cantidad de paquetes enviados simultáneamente y el número de reintentos para los puertos escaneados.

## Timeouts

Cuando Nmap envía un paquete, toma un tiempo (Round-Trip-Time - RTT) recibir una respuesta del puerto escaneado. Generalmente, Nmap comienza con un timeout alto (`--min-rtt-timeout`) de 100 ms. Veamos un ejemplo de escaneo en toda una red con 256 hosts, incluidos los 100 puertos principales.

#### Escaneo por Defecto

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F
```

* **Tiempo de escaneo**: 39.44 segundos

#### Escaneo Optimizado

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms
```

* **Tiempo de escaneo**: 12.29 segundos

#### **Opciones de Escaneo**

* `10.129.2.0/24`: Escanea la red objetivo especificada.
* `-F`: Escanea los 100 puertos principales.
* `--initial-rtt-timeout 50ms`: Establece el tiempo de espera inicial RTT.
* `--max-rtt-timeout 100ms`: Establece el tiempo máximo de espera RTT.

#### Conclusión

Comparando los dos escaneos, encontramos que el escaneo optimizado encontró dos hosts menos, pero el tiempo de escaneo fue solo una cuarta parte del tiempo del escaneo por defecto. Esto indica que ajustar el timeout inicial RTT a un periodo demasiado corto puede hacer que pasemos por alto algunos hosts.

## Max Retries

Otra forma de aumentar la velocidad del escaneo es especificar la tasa de reintentos de los paquetes enviados (`--max-retries`). El valor por defecto para los reintentos es 10, por lo que si Nmap no recibe una respuesta para un puerto, no enviará más paquetes al puerto y se saltará.

#### Escaneo por Defecto

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F | grep "/tcp" | wc -l
```

* **Puertos abiertos encontrados**: 23

#### Escaneo con Reintentos Reducidos

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l
```

* **Puertos abiertos encontrados**: 21

#### **Opciones de Escaneo**

* `10.129.2.0/24`: Escanea la red objetivo especificada.
* `-F`: Escanea los 100 puertos principales.
* `--max-retries 0`: Establece el número de reintentos durante el escaneo.

#### Conclusión

Acelerar el escaneo también puede tener un efecto negativo en los resultados, lo que significa que podemos pasar por alto información importante.

## Rates

Durante una prueba de penetración en un entorno conocido, podemos estar autorizados para probar los sistemas de la red y no solo probar las medidas de protección. Si conocemos el ancho de banda de la red, podemos ajustar la tasa de paquetes enviados, lo que puede acelerar significativamente nuestros escaneos con Nmap. Al establecer la tasa mínima (`--min-rate <number>`) para enviar paquetes, indicamos a Nmap que envíe el número especificado de paquetes simultáneamente. Intentará mantener esta tasa en consecuencia.

#### Escaneo por Defecto

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.default
```

* **Tiempo de escaneo**: 29.83 segundos

#### Escaneo Optimizado

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300
```

* **Tiempo de escaneo**: 8.67 segundos

#### **Opciones de Escaneo**

* `10.129.2.0/24`: Escanea la red objetivo especificada.
* `-F`: Escanea los 100 puertos principales.
* `-oN tnet.minrate300`: Guarda los resultados en un formato normal, comenzando con el nombre de archivo especificado.
* `--min-rate 300`: Establece el número mínimo de paquetes a enviar por segundo.

#### Conclusión

El escaneo optimizado encontró la misma cantidad de puertos abiertos que el escaneo por defecto, pero el tiempo de escaneo fue considerablemente menor.

## Timing

Dado que tales configuraciones no siempre se pueden optimizar manualmente, como en una prueba de penetración de caja negra, Nmap ofrece seis plantillas de tiempo diferentes (`-T <0-5>`) para usar. Estos valores (0-5) determinan la agresividad de nuestros escaneos. Esto también puede tener efectos negativos si el escaneo es demasiado agresivo y los sistemas de seguridad pueden bloquearnos debido al tráfico de red generado. La plantilla de tiempo por defecto utilizada cuando no se ha definido nada más es la normal (`-T 3`).

#### Escaneo por Defecto

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.default
```

* **Tiempo de escaneo**: 32.44 segundos

#### Escaneo Insano

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T 5
```

* **Tiempo de escaneo**: 18.07 segundos

**Opciones de Escaneo**

* `10.129.2.0/24`: Escanea la red objetivo especificada.
* `-F`: Escanea los 100 puertos principales.
* `-oN tnet.T5`: Guarda los resultados en un formato normal, comenzando con el nombre de archivo especificado.
* `-T 5`: Especifica la plantilla de tiempo insana.

#### Conclusión

El escaneo insano encontró la misma cantidad de puertos abiertos que el escaneo por defecto, pero el tiempo de escaneo fue considerablemente menor.

Para más información sobre el rendimiento del escaneo, puedes consultar [este enlace](https://nmap.org/book/man-performance.html).
