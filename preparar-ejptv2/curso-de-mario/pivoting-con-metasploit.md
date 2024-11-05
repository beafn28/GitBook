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

Con estas configuraciones, habrás creado un entorno de red adecuado para realizar pruebas de pentesting, donde Windows 7 y Metasploitable2 pueden comunicarse entre sí a través de la red NAT, mientras que Kali Linux tendrá acceso a ambas redes: la interna (NAT) y la red externa (a través del adaptador puente).

## Arranque y Verificación de Máquinas

Sigue estos pasos para arrancar las máquinas y verificar las configuraciones de red.

### 1. Orden de Arranque

1. **Kali Linux**: Inicia primero.
2. **Windows 7**: Luego, enciende la máquina víctima.
3. **Metasploitable2**: Finalmente, arranca la máquina vulnerable.

### 2. Comprobaciones de Red

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

Con estos pasos, habrás confirmado que todas las máquinas están configuradas correctamente para tu entorno de pruebas.

#### Infiltración en la Máquina Windows7 y Pivoting

### 1. Escaneo de Interfaces de Red con `arp-scan`

Para identificar las máquinas en la red local:

```bash
arp-scan -I eth0 --localnet
```

