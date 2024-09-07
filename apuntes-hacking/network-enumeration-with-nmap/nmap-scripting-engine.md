# Nmap Scripting Engine

El **Nmap Scripting Engine (NSE)** es una característica útil de Nmap que nos permite crear scripts en **Lua** para interactuar con servicios específicos. Los scripts se dividen en **14 categorías** diferentes:

## Categorías de scripts en NSE

| **Categoría** | **Descripción**                                                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **auth**      | Determina credenciales de autenticación.                                                                                                    |
| **broadcast** | Scripts usados para descubrimiento de hosts mediante broadcasting. Los hosts descubiertos se pueden agregar automáticamente a los escaneos. |
| **brute**     | Realiza ataques de fuerza bruta para iniciar sesión en el servicio correspondiente.                                                         |
| **default**   | Scripts predeterminados ejecutados con la opción `-sC`.                                                                                     |
| **discovery** | Evalúa servicios accesibles.                                                                                                                |
| **dos**       | Verifica vulnerabilidades de denegación de servicio (DoS).                                                                                  |
| **exploit**   | Intenta explotar vulnerabilidades conocidas.                                                                                                |
| **external**  | Usa servicios externos para procesamiento adicional.                                                                                        |
| **fuzzer**    | Envía campos diferentes para identificar vulnerabilidades. Puede tardar mucho tiempo.                                                       |
| **intrusive** | Scripts intrusivos que podrían afectar negativamente al sistema objetivo.                                                                   |
| **malware**   | Verifica si el sistema está infectado con malware.                                                                                          |
| **safe**      | Scripts defensivos que no realizan accesos intrusivos o destructivos.                                                                       |
| **version**   | Extiende la detección de servicios.                                                                                                         |
| **vuln**      | Identifica vulnerabilidades específicas.                                                                                                    |

### Formas de ejecutar scripts NSE

#### Scripts predeterminados

Se pueden ejecutar los scripts predeterminados de NSE utilizando el siguiente comando:

```bash
sudo nmap <objetivo> -sC
```

#### Ejecutar scripts por categoría

Es posible ejecutar todos los scripts de una categoría específica de NSE:

```bash
sudo nmap <objetivo> --script <categoría>
```

#### Ejecutar scripts específicos

Para ejecutar scripts específicos, se puede usar el siguiente comando:

```bash
sudo nmap <objetivo> --script <nombre-script>,<nombre-script>,...
```

#### Ejemplo de uso con scripts específicos

Supongamos que queremos escanear el puerto **25** (SMTP) de un objetivo y utilizar los scripts `banner` y `smtp-commands`:

```bash
sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands
```

**Salida del escaneo:**

```makefile
PORT   STATE SERVICE
25/tcp open  smtp
|_banner: 220 inlane ESMTP Postfix (Ubuntu)
|_smtp-commands: inlane, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
```

#### Explicación del ejemplo:

* **banner**: Muestra el banner del servicio SMTP, identificando que es un servidor **Postfix en Ubuntu**.
* **smtp-commands**: Muestra los comandos que el servidor SMTP admite, lo que puede ayudar a descubrir usuarios existentes.

### Escaneo agresivo

El escaneo agresivo (`-A`) utiliza múltiples opciones como:

* **Detección de servicios (-sV)**
* **Detección de sistema operativo (-O)**
* **Traceroute (--traceroute)**
* **Scripts predeterminados (-sC)**

```bash
sudo nmap 10.129.2.28 -p 80 -A
```

**Salida del escaneo:**

```makefile
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.3.4
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: blog.inlanefreight.com
```

#### Información obtenida:

* **Servidor web**: Apache 2.4.29 en Ubuntu.
* **Aplicación web**: WordPress 5.3.4.
* **Título de la página web**: blog.inlanefreight.com.
* **Sistema operativo**: Probablemente **Linux** (96%).

### Evaluación de vulnerabilidades

Se pueden identificar vulnerabilidades utilizando scripts de la categoría **vuln**. A continuación, se muestra un ejemplo de escaneo en el puerto 80 de un objetivo:

```bash
sudo nmap 10.129.2.28 -p 80 -sV --script vuln
```

**Salida del escaneo:**

```bash
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-enum:
|   /wp-login.php: Possible admin folder
|   /readme.html: Wordpress version: 2
|   /: WordPress version: 5.3.4
| http-wordpress-users:
| Username found: admin
| vulners:
|   cpe:/a:apache:http_server:2.4.29:
|      CVE-2019-0211   7.2   https://vulners.com/cve/CVE-2019-0211
|      CVE-2018-1312   6.8   https://vulners.com/cve/CVE-2018-1312
```

#### Información obtenida:

* **WordPress**: Identifica múltiples versiones de WordPress y posibles archivos sensibles como `wp-login.php` y `readme.html`.
* **Vulnerabilidades**: Identifica vulnerabilidades conocidas en el servidor Apache con enlaces a bases de datos de CVEs.

### Referencias adicionales

Para más información sobre los scripts de NSE y sus categorías, puedes visitar la [documentación de NSE](https://nmap.org/nsedoc/index.html).
