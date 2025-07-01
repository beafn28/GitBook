---
icon: '1'
---

# Presentación + Preparación de Laboratorios

## Distribuciones de Hacking

* [Kali Linux ](https://youtu.be/pBjsV6QWObY?si=mIlsh3ByltdgbKZz)
* [Parrot](https://youtu.be/ipMwsvyLb6E?si=UXQNfy9xFyywY5SS)

## Redes

### Adaptador puente

En esta configuración, la máquina virtual se conecta directamente a la red física local (LAN), recibiendo su propia dirección IP. Es como si fuera un dispositivo más en la red, y puede interactuar libremente con otros dispositivos.

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Nat

Es una configuración de red que permite a la VM acceder a Internet a través del host, sin interactuar directamente con otros dispositivos en la red local. Es seguro y fácil de configurar, pero limita la capacidad de la VM para realizar pruebas de penetración en la red local o recibir tráfico entrante. Es ideal para tareas que solo requieren acceso a Internet o para entornos donde no necesitas que la máquina virtual sea visible en la red.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (8).png" alt=""><figcaption></figcaption></figure>

### Red Interna

Es una configuración de red en máquinas virtuales que permite que varias máquinas virtuales se comuniquen entre sí dentro de un entorno aislado, sin tener acceso a la red externa o a Internet. Este tipo de configuración es útil cuando deseas crear un laboratorio de pruebas donde las máquinas se comuniquen de manera privada, sin interferir con la red física del host ni con la red local (LAN).

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (1).png" alt=""><figcaption></figcaption></figure>

### Tipos de Redes

<table data-header-hidden data-full-width="true"><thead><tr><th></th><th></th><th></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><td><strong>Clase de Red</strong></td><td><strong>Rango de Direcciones IP</strong></td><td><strong>Primera IP Usable</strong></td><td><strong>Última IP Usable</strong></td><td><strong>Máscara de Red</strong></td><td><strong>CIDR</strong></td><td><strong>Cantidad de Hosts</strong></td></tr><tr><td><strong>Clase A</strong></td><td>0.0.0.0 - 127.255.255.255</td><td>1.0.0.1</td><td>126.255.255.254</td><td>255.0.0.0</td><td>/8</td><td>16,777,214</td></tr><tr><td><strong>Clase B</strong></td><td>128.0.0.0 - 191.255.255.255</td><td>128.0.0.1</td><td>191.255.255.254</td><td>255.255.0.0</td><td>/16</td><td>65,534</td></tr><tr><td><strong>Clase C</strong></td><td>192.0.0.0 - 223.255.255.255</td><td>192.0.0.1</td><td>223.255.255.254</td><td>255.255.255.0</td><td>/24</td><td>254</td></tr></tbody></table>
