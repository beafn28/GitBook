# Scanning Issues

**Nessus** es una plataforma de escaneo de vulnerabilidades bien conocida y ampliamente utilizada. Sin embargo, es importante tener en cuenta algunas **mejores prácticas** antes de iniciar un escaneo. Los escaneos pueden causar problemas en redes sensibles, generar falsos positivos, no devolver resultados o tener un impacto negativo en la red. Siempre es recomendable comunicarte con tu cliente (o partes interesadas internas si ejecutas el escaneo en tu propia red) para verificar si hay hosts sensibles/legados que deban ser excluidos o si algún host de alta prioridad/disponibilidad debe ser escaneado por separado, fuera de las horas laborales habituales o con configuraciones de escaneo diferentes para evitar problemas potenciales.

A veces, un escaneo puede devolver resultados inesperados y necesitar ajustes.

## **Mitigación de Problemas**

Algunos cortafuegos (firewalls) pueden hacer que los resultados del escaneo muestren que **todos los puertos están abiertos** o **ningún puerto está abierto**. En estos casos, una solución rápida es configurar un **Escaneo Avanzado** y desactivar la opción **Ping al host remoto**. Esto evita que el escaneo use ICMP para verificar si el host está "vivo" y, en su lugar, continúa con el escaneo. Algunos firewalls pueden devolver un mensaje "ICMP Unreachable" que Nessus interpretará como un host activo, generando falsos positivos informacionales.

En redes sensibles, se puede usar **limitación de tasa** para minimizar el impacto. Por ejemplo, podemos ajustar las opciones de rendimiento y modificar el parámetro **Max Concurrent Checks Per Host** si el host objetivo suele estar bajo una carga elevada, como en una aplicación web de uso intensivo. Esto limitará la cantidad de plugins utilizados de manera concurrente en el host.

Podemos evitar escanear sistemas heredados (legacy systems) y elegir la opción de **no escanear impresoras**, como se mencionó en una sección anterior. Si un host es de particular preocupación, debería quedar fuera del alcance del escaneo o podemos usar el archivo **nessusd.rules** para configurar los escaneos de Nessus. Puedes encontrar más información sobre este archivo aquí.

Finalmente, a menos que se solicite específicamente, **nunca debemos realizar verificaciones de Denegación de Servicio (DoS)**. Podemos asegurarnos de que este tipo de plugins no se utilicen activando siempre la opción **safe checks** al realizar escaneos, para evitar cualquier plugin de red que pueda tener un impacto negativo en un objetivo, como el bloqueo de un daemon de red. Activar la opción de **safe checks** no garantiza que un escaneo de vulnerabilidades con Nessus no tenga ningún impacto negativo, pero sí minimiza significativamente el posible impacto y reduce el tiempo de escaneo.

Es crucial **comunicarnos** con nuestros clientes o partes interesadas internas y alertar al personal necesario antes de comenzar un escaneo. Cuando el escaneo esté completo, debemos llevar un registro detallado de la actividad de escaneo en caso de que ocurra un incidente que deba investigarse.

## Impacto en la Red

Es importante tener en cuenta el **impacto potencial** de un escaneo de vulnerabilidades en una red, especialmente en enlaces con bajo ancho de banda o congestionados. Esto se puede medir utilizando **vnstat**:

```bash
sherlock28@htb[/htb]$ sudo apt install vnstat
```

Vamos a monitorear el adaptador de red **eth0** antes de ejecutar un escaneo de Nessus:

```bash
sherlock28@htb[/htb]$ sudo vnstat -l -i eth0

Monitoring eth0...    (press CTRL-C to stop)

   rx:       332 bit/s     0 p/s          tx:       332 bit/s     0 p/s

   rx:         0 bit/s     0 p/s          tx:         0 bit/s     0 p/s^C
```

**Estadísticas de tráfico de eth0:**

|             | rx          | tx        |
| ----------- | ----------- | --------- |
| **bytes**   | 572 B       | 392 B     |
| **max**     | 480 bit/s   | 332 bit/s |
| **average** | 114 bit/s   | 78 bit/s  |
| **packets** | 8           | 5         |
| **time**    | 40 segundos |           |

Ahora, comparamos este resultado con el monitoreo de la misma interfaz durante un escaneo de Nessus contra un solo host:

```bash
sherlock28@htb[/htb]$ sudo vnstat -l -i eth0

Monitoring eth0...    (press CTRL-C to stop)

   rx:   307.92 kbit/s   641 p/s          tx:   380.41 kbit/s   767 p/s^C
```

**Estadísticas de tráfico de eth0 durante el escaneo:**

|             | rx            | tx            |
| ----------- | ------------- | ------------- |
| **bytes**   | 1.04 MiB      | 1.34 MiB      |
| **max**     | 414.81 kbit/s | 480.59 kbit/s |
| **average** | 230.57 kbit/s | 296.72 kbit/s |
| **packets** | 18252         | 22733         |
| **time**    | 38 segundos   |               |

Al comparar los resultados, podemos ver que la cantidad de bytes y paquetes transferidos durante un escaneo de vulnerabilidades es bastante significativa y puede tener un impacto severo en una red si no se ajusta correctamente o si se realiza contra dispositivos frágiles/sensibles.
