# Domain Information

### Introducción

La **información de dominio** es crucial en cualquier prueba de penetración. No se refiere solo a los subdominios, sino a toda la presencia en línea de una empresa. La recopilación de esta información se realiza de manera pasiva, es decir, sin escaneos directos ni interacción activa con los sistemas de la empresa, para evitar ser detectados.

### Estrategias de Recopilación

1. **Análisis del Sitio Web Principal**:
   * **Objetivo**: Entender la funcionalidad y las tecnologías utilizadas por la empresa.
   * **Ejemplo**: Empresas de TI pueden ofrecer servicios como desarrollo de aplicaciones, IoT, hosting, etc. Familiarízate con estos servicios para entender la estructura y tecnología de la empresa.
2.  **Uso de Certificados SSL**:

    * **Fuente**: [crt.sh](https://crt.sh)
    * **Método**: Los certificados SSL a menudo incluyen múltiples subdominios. Puedes buscar en crt.sh para obtener una lista de subdominios asociados a un dominio.

    ```bash
    curl -s "https://crt.sh/?q=inlanefreight.com&output=json" | jq .
    ```

    * **Filtrar Subdominios Únicos**:

    ```bash
    curl -s "https://crt.sh/?q=inlanefreight.com&output=json" | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u
    ```
3.  **Identificación de Hosts Activos**:

    * **Método**: Usa `host` para determinar las direcciones IP asociadas a los subdominios.

    ```bash
    for i in $(cat subdomainlist); do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4; done
    ```
4.  **Exploración con Shodan**:

    * **Objetivo**: Encontrar dispositivos y servicios conectados a Internet.
    * **Ejemplo de Comando**:

    ```bash
    for i in $(cat ip-addresses.txt); do shodan host $i; done
    ```
5.  **Consulta de Registros DNS**:

    * **Método**: Usa `dig` para obtener registros DNS y analizar la infraestructura.

    ```bash
    dig any inlanefreight.com
    ```

    * **Registros Clave**:
      * **A Registros**: Direcciones IP.
      * **MX Registros**: Servidores de correo (p.ej., Google).
      * **NS Registros**: Servidores de nombres.
      * **TXT Registros**: Información sobre verificación de dominios y seguridad.

### Análisis de Resultados

* **Certificados SSL**: Proporcionan subdominios y posibles configuraciones.
* **Shodan**: Revela dispositivos conectados y puertos abiertos.
* **Registros DNS**: Ofrecen información sobre infraestructura de red y servicios externos.

**Ejemplo**: Los registros TXT pueden mostrar que la empresa usa servicios como Atlassian, Google, LogMeIn, Mailgun, y Outlook, lo cual indica las herramientas y proveedores asociados con la empresa. Esto puede ayudar a identificar posibles vectores de ataque y áreas para investigar más a fondo.
