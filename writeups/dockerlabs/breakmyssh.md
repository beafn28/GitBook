---
description: >-
  ✍️ Autor: El Pingüino de Mario🔍 Dificultad: Muy Fácil  📅 Fecha de creación:
  01/01/2000
---

# BreakMySSH

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 172.17.0.2
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/0 (9).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -sVC -p- -n --min-rate 5000 172.17.0.2
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

![](<../../.gitbook/assets/1 (10).png>)

Como podemos observar durante el escaneo que el **puerto 22** perteneciente al **servicio SSH** está abierto por lo que a continuación se indagará más.

### 🚀 **EXPLOTACIÓN**

Con la información obtenida anteriormente, realizamos el comando:

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 5
```

Este comando utiliza la herramienta **Hydra** para realizar un ataque de fuerza bruta contra el servicio SSH de una máquina con la IP **172.17.0.2**.

![](<../../.gitbook/assets/2 (10).png>)

Al realizar el ataque de fuerza bruta, descubrimos la contraseña de **root**. Sabiendo esto, nos conectamos a través de SSH al usuario con el comando:

```bash
ssh root@172.17.0.2
```

![](<../../.gitbook/assets/3 (10).png>)

### 🔐 **PRIVILEGIOS**

Como se puede observar anteriormente, al descubrir la contraseña de **root**, tenemos los máximos privilegios. Esto se puede verificar al ejecutar:

```bash
whoami
```

y confirmar que somos **root**.
