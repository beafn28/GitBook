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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Nat

Es una configuración de red que permite a la VM acceder a Internet a través del host, sin interactuar directamente con otros dispositivos en la red local. Es seguro y fácil de configurar, pero limita la capacidad de la VM para realizar pruebas de penetración en la red local o recibir tráfico entrante. Es ideal para tareas que solo requieren acceso a Internet o para entornos donde no necesitas que la máquina virtual sea visible en la red.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Red Interna

Es una configuración de red en máquinas virtuales que permite que varias máquinas virtuales se comuniquen entre sí dentro de un entorno aislado, sin tener acceso a la red externa o a Internet. Este tipo de configuración es útil cuando deseas crear un laboratorio de pruebas donde las máquinas se comuniquen de manera privada, sin interferir con la red física del host ni con la red local (LAN).

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

