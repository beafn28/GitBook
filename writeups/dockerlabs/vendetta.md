---
description: '✍️ Autor: misk0z🔍 Dificultad: Fácil  📅 Fecha de creación: 16/10/2024'
---

# Vendetta

### 🔍 RECONOCIMIENTO

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

A continuación, realizamos el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (942).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH,** el **puerto 80** perteneciente al **servicio HTTP** y el **puerto 3306** perteneciente al **servicio MySQL** están abiertos por lo que a continuación se indagará más.&#x20;

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p22,80,3306 -v 172.17.0.2
```

para obtener más información sobre esos puertos específicamente.

<figure><img src="../../.gitbook/assets/image (946).png" alt=""><figcaption></figcaption></figure>

Seguimos indagando más sobre los puertos y ahora indagamos sobre el **servicio HTTP**. Se ingresó la **dirección IP** en el navegador lo que llevó a que la página web sea una página sobre alguien anónimo.

<figure><img src="../../.gitbook/assets/image (947).png" alt=""><figcaption></figcaption></figure>

Revisando el código fuente me encuentro un detalle que me resulta la atención ya que lo tiene como una **strong** la **V** por lo que supongo que es un usuario.

<figure><img src="../../.gitbook/assets/image (948).png" alt=""><figcaption></figcaption></figure>

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l V -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

que utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio **MySQL** de una máquina con la IP **172.17.0.2**. Observamos que tarda demasiado por lo que debe ser de las últimas contraseñas del diccionario por lo que realizamos los siguientes comandos.\
Volteamos el diccionario.

```bash
tac /usr/share/wordlists/rockyou.txt > diccionario
```

Quitamos espacios.

```bash
cat diccionario | sed 's/ //g' > rockyou
```

Realizamos el ataque de fuerza bruta con **Hydra** con ese diccionario creado.

