# DNS

El Sistema de Nombres de Dominio (DNS) actúa como el GPS de Internet, guiando tu viaje en línea desde hitos memorables (nombres de dominio) hasta coordenadas numéricas precisas (direcciones IP). Al igual que el GPS traduce un nombre de destino en latitud y longitud para la navegación, el DNS traduce nombres de dominio legibles por humanos (como [www.ejemplo.com](http://www.ejemplo.com)) en las direcciones IP numéricas (como 192.0.2.1) que las computadoras usan para comunicarse.

Imagina navegar por una ciudad memorizando la latitud y longitud exactas de cada lugar que quieres visitar. Sería increíblemente complicado e ineficiente. El DNS elimina esta complejidad al permitirnos usar nombres de dominio fáciles de recordar en lugar de números. Cuando escribes un nombre de dominio en tu navegador, el DNS actúa como tu guía, encontrando rápidamente la dirección IP correspondiente y dirigiendo tu solicitud al destino correcto en Internet.

Sin DNS, navegar por el mundo en línea sería como conducir sin un mapa o GPS: una tarea frustrante y propensa a errores.

### Cómo Funciona el DNS

Imagina que quieres visitar un sitio web como [www.ejemplo.com](http://www.ejemplo.com). Escribes este nombre de dominio amigable en tu navegador, pero tu computadora no entiende palabras: habla el lenguaje de los números, específicamente las direcciones IP. Entonces, ¿cómo encuentra tu computadora la dirección IP del sitio web? Aquí entra el DNS, el traductor confiable de Internet.

<figure><img src="../../.gitbook/assets/image (656).png" alt=""><figcaption></figcaption></figure>

1. **Tu Computadora Pide Direcciones (Consulta DNS)**: Cuando ingresas el nombre de dominio, tu computadora primero revisa su memoria (caché) para ver si recuerda la dirección IP de una visita anterior. Si no, se dirige a un resolvedor DNS, que generalmente es proporcionado por tu proveedor de servicios de Internet (ISP).
2. **El Resolvedor DNS Revisa su Mapa (Búsqueda Recursiva)**: El resolvedor también tiene una caché, y si no encuentra la dirección IP allí, comienza un viaje a través de la jerarquía del DNS. Empieza preguntando a un servidor de nombres raíz, que es como el bibliotecario de Internet.
3. **El Servidor de Nombres Raíz Señala el Camino**: El servidor raíz no conoce la dirección exacta, pero sabe quién sí lo hace: el servidor de nombres de Dominio de Nivel Superior (TLD) responsable del final del dominio (por ejemplo, .com, .org). Indica al resolvedor la dirección correcta.
4. **El Servidor de Nombres TLD Reduce la Búsqueda**: El servidor de nombres TLD es como un mapa regional. Sabe qué servidor de nombres autorizado es responsable del dominio específico que estás buscando (por ejemplo, ejemplo.com) y envía al resolvedor allí.
5. **El Servidor de Nombres Autorizado Entrega la Dirección**: El servidor de nombres autorizado es la última parada. Es como la dirección exacta del sitio web que deseas. Tiene la dirección IP correcta y la envía de vuelta al resolvedor.
6. **El Resolvedor DNS Devuelve la Información**: El resolvedor recibe la dirección IP y se la da a tu computadora. También la recuerda por un tiempo (la guarda en caché), en caso de que quieras volver a visitar el sitio web pronto.
7. **Tu Computadora Conecta**: Ahora que tu computadora conoce la dirección IP, puede conectarse directamente al servidor web que aloja el sitio web, y puedes comenzar a navegar.

## El Archivo Hosts

El archivo hosts es un archivo de texto simple utilizado para mapear nombres de host a direcciones IP, proporcionando un método manual de resolución de nombres de dominio que evita el proceso de DNS. Mientras que el DNS automatiza la traducción de nombres de dominio a direcciones IP, el archivo hosts permite ajustes directos y locales. Esto puede ser especialmente útil para desarrollo, solución de problemas o bloqueo de sitios web.

El archivo hosts se encuentra en `C:\Windows\System32\drivers\etc\hosts` en Windows y en `/etc/hosts` en Linux y MacOS. Cada línea en el archivo sigue el formato:

```txt
<Dirección IP>    <Nombre de Host> [<Alias> ...]
```

Por ejemplo:

```txt
127.0.0.1       localhost
192.168.1.10    devserver.local
```

Para editar el archivo hosts, ábrelo con un editor de texto utilizando privilegios administrativos/root. Agrega nuevas entradas según sea necesario y guarda el archivo. Los cambios se aplican de inmediato sin necesidad de reiniciar el sistema.

Usos comunes incluyen redirigir un dominio a un servidor local para desarrollo:

```txt
127.0.0.1       myapp.local
```

Probar la conectividad especificando una dirección IP:

```txt
192.168.1.20    testserver.local
```

O bloquear sitios web no deseados redirigiendo sus dominios a una dirección IP inexistente:

```txt
0.0.0.0       unwanted-site.com
```

### Es Como una Carrera de Relevos

Piensa en el proceso de DNS como una carrera de relevos. Tu computadora comienza con el nombre de dominio y lo pasa al resolvedor. El resolvedor luego pasa la solicitud al servidor raíz, al servidor TLD, y finalmente al servidor autorizado, acercándose cada vez más al destino. Una vez que se encuentra la dirección IP, se retransmite a tu computadora, permitiéndote acceder al sitio web.

### Conceptos Clave del DNS

En el Sistema de Nombres de Dominio (DNS), una zona es una parte distinta del espacio de nombres del dominio que un entidad o administrador específico gestiona. Piensa en ella como un contenedor virtual para un conjunto de nombres de dominio. Por ejemplo, `example.com` y todos sus subdominios (como `mail.example.com` o `blog.example.com`) normalmente pertenecerían a la misma zona de DNS.

El archivo de zona, un archivo de texto que reside en un servidor DNS, define los registros de recursos (discutidos a continuación) dentro de esta zona, proporcionando información crucial para traducir nombres de dominio en direcciones IP.

Para ilustrar, aquí tienes un ejemplo simplificado de cómo podría verse un archivo de zona para `example.com`:

```zone
zoneCopiar código$TTL 3600 ; Tiempo de Vida por Defecto (1 hora)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Número de Serie (YYYYMMDDNN)
                3600       ; Intervalo de Actualización
                900        ; Intervalo de Reintento
                604800     ; Tiempo de Expiración
                86400 )    ; TTL Mínimo

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

Este archivo define los servidores de nombres autorizados (registros NS), el servidor de correo (registro MX) y las direcciones IP (registros A) para varios hosts dentro del dominio `example.com`.

### Conceptos de Registros DNS

Los servidores DNS almacenan varios registros de recursos, cada uno con un propósito específico en el proceso de resolución de nombres de dominio. Aquí exploramos algunos de los conceptos más comunes de DNS:

| Concepto DNS                       | Descripción                                                                                  | Ejemplo                                                                     |
| ---------------------------------- | -------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Nombre de Dominio**              | Una etiqueta legible por humanos para un sitio web u otro recurso en internet.               | [www.example.com](http://www.example.com)                                   |
| **Dirección IP**                   | Un identificador numérico único asignado a cada dispositivo conectado a Internet.            | 192.0.2.1                                                                   |
| **Resolvedor DNS**                 | Un servidor que traduce nombres de dominio en direcciones IP.                                | El servidor DNS de tu ISP o resolvedores públicos como Google DNS (8.8.8.8) |
| **Servidor de Nombres Raíz**       | Los servidores de nivel superior en la jerarquía DNS.                                        | Hay 13 servidores raíz en todo el mundo, nombrados A-M: a.root-servers.net  |
| **Servidor de Nombres TLD**        | Servidores responsables de dominios de nivel superior específicos (por ejemplo, .com, .org). | Verisign para .com, PIR para .org                                           |
| **Servidor de Nombres Autorizado** | El servidor que tiene la dirección IP real para un dominio.                                  | A menudo gestionado por proveedores de hosting o registradores de dominios. |
| **Tipos de Registros DNS**         | Diferentes tipos de información almacenada en DNS.                                           | A, AAAA, CNAME, MX, NS, TXT, etc.                                           |

Ahora que hemos explorado los conceptos fundamentales de DNS, profundicemos en los bloques de construcción de la información DNS: los diversos tipos de registros. Estos registros almacenan diferentes tipos de datos asociados con nombres de dominio, cada uno con un propósito específico:

| Tipo de Registro | Nombre Completo                   | Descripción                                                                                                                                                         | Ejemplo de Archivo de Zona                                                                   |
| ---------------- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **A**            | Registro de Dirección             | Mapea un nombre de host a su dirección IPv4.                                                                                                                        | `www.example.com. IN A 192.0.2.1`                                                            |
| **AAAA**         | Registro de Dirección IPv6        | Mapea un nombre de host a su dirección IPv6.                                                                                                                        | `www.example.com. IN AAAA 2001:db8:85a3::8a2e:370:7334`                                      |
| **CNAME**        | Registro de Nombre Canónico       | Crea un alias para un nombre de host, apuntando a otro nombre de host.                                                                                              | `blog.example.com. IN CNAME webserver.example.net.`                                          |
| **MX**           | Registro de Intercambio de Correo | Especifica los servidores de correo responsables de manejar el correo para el dominio.                                                                              | `example.com. IN MX 10 mail.example.com.`                                                    |
| **NS**           | Registro de Servidor de Nombres   | Delegar una zona DNS a un servidor de nombres autorizado específico.                                                                                                | `example.com. IN NS ns1.example.com.`                                                        |
| **TXT**          | Registro de Texto                 | Almacena información textual arbitraria, a menudo utilizada para verificación de dominio o políticas de seguridad.                                                  | `example.com. IN TXT "v=spf1 mx -all"` (registro SPF)                                        |
| **SOA**          | Registro de Inicio de Autoridad   | Especifica la información administrativa sobre una zona DNS, incluyendo el servidor de nombres principal, el correo electrónico del responsable y otros parámetros. | `example.com. IN SOA ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400` |
| **SRV**          | Registro de Servicio              | Define el nombre de host y el número de puerto para servicios específicos.                                                                                          | `_sip._udp.example.com. IN SRV 10 5 5060 sipserver.example.com.`                             |
| **PTR**          | Registro de Puntero               | Usado para búsquedas inversas de DNS, mapea una dirección IP a un nombre de host.                                                                                   | `1.2.0.192.in-addr.arpa. IN PTR www.example.com.`                                            |

El "IN" en los ejemplos significa "Internet". Es un campo de clase en los registros DNS que especifica la familia de protocolos. En la mayoría de los casos, verás "IN" utilizado, ya que denota el suite de protocolos de Internet (IP) utilizada para la mayoría de los nombres de dominio. Existen otros valores de clase (por ejemplo, CH para Chaosnet, HS para Hesiod) pero rara vez se utilizan en configuraciones modernas de DNS.

En esencia, "IN" es simplemente una convención que indica que el registro se aplica a los protocolos de internet estándar que usamos hoy en día. Aunque puede parecer un detalle adicional, entender su significado proporciona una comprensión más profunda de la estructura de los registros DNS.

## La Importancia del DNS para la Reconocimiento Web

El DNS no es simplemente un protocolo técnico para traducir nombres de dominio; es un componente crítico de la infraestructura de un objetivo que puede ser utilizado para descubrir vulnerabilidades y obtener acceso durante una prueba de penetración:

### **Descubriendo Activos**

Los registros DNS pueden revelar una gran cantidad de información, incluyendo subdominios, servidores de correo y registros de servidores de nombres. Por ejemplo, un registro CNAME que apunte a un servidor desactualizado (por ejemplo, `dev.example.com CNAME oldserver.example.net`) podría llevar a un sistema vulnerable.

### **Mapeo de la Infraestructura de Red**

Puedes crear un mapa completo de la infraestructura de red del objetivo analizando los datos de DNS. Por ejemplo, identificar los servidores de nombres (registros NS) para un dominio puede revelar el proveedor de alojamiento utilizado, mientras que un registro A para `loadbalancer.example.com` puede señalar un balanceador de carga. Esto te ayuda a entender cómo están conectados los diferentes sistemas, identificar el flujo de tráfico y localizar posibles puntos críticos o debilidades que podrían ser explotados durante una prueba de penetración.

### **Monitoreo de Cambios**

Monitorear continuamente los registros DNS puede revelar cambios en la infraestructura del objetivo con el tiempo. Por ejemplo, la aparición repentina de un nuevo subdominio (como `vpn.example.com`) podría indicar un nuevo punto de entrada en la red, mientras que un registro TXT que contenga un valor como `_1password=...` sugiere fuertemente que la organización está utilizando 1Password, lo que podría ser aprovechado para ataques de ingeniería social o campañas de phishing dirigidas.
