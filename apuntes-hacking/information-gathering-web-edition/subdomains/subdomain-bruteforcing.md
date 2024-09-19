# Subdomain Bruteforcing

La **enumeración de subdominios por fuerza bruta** es una poderosa técnica activa de descubrimiento que aprovecha listas predefinidas de nombres de subdominios potenciales. Este enfoque prueba sistemáticamente estos nombres contra el dominio objetivo para identificar subdominios válidos. Usando listas de palabras bien elaboradas, puedes aumentar significativamente la eficiencia y efectividad en la búsqueda de subdominios.

**El proceso se divide en cuatro pasos:**

1. **Selección de la lista de palabras (Wordlist):** El proceso comienza seleccionando una lista de palabras que contiene posibles nombres de subdominios. Estas listas pueden ser:
   * **Generales:** Con una amplia gama de nombres comunes de subdominios (por ejemplo, `dev`, `staging`, `blog`, `mail`, `admin`, `test`). Este enfoque es útil cuando no conoces los patrones de nombres del objetivo.
   * **Dirigidas:** Enfocadas en industrias específicas, tecnologías o patrones de nombres relevantes para el objetivo, lo que aumenta la eficiencia y reduce la posibilidad de falsos positivos.
   * **Personalizadas:** Puedes crear tu propia lista de palabras basada en palabras clave específicas, patrones o inteligencia reunida de otras fuentes.
2. **Iteración y consultas:** Un script o herramienta recorre la lista de palabras, anexando cada palabra o frase al dominio principal (por ejemplo, `example.com`) para crear posibles nombres de subdominios (por ejemplo, `dev.example.com`, `staging.example.com`).
3. **Consulta DNS:** Se realiza una consulta DNS para cada subdominio potencial para verificar si se resuelve en una dirección IP. Esto se suele hacer utilizando registros de tipo A o AAAA.
4. **Filtrado y validación:** Si un subdominio se resuelve correctamente, se añade a una lista de subdominios válidos. Se pueden realizar pasos adicionales para validar su existencia y funcionalidad (por ejemplo, intentando acceder a él a través de un navegador web).

#### Herramientas para la enumeración por fuerza bruta de subdominios:

| Herramienta     | Descripción                                                                                      |
| --------------- | ------------------------------------------------------------------------------------------------ |
| **dnsenum**     | Herramienta completa de enumeración DNS que admite ataques por diccionario y fuerza bruta.       |
| **fierce**      | Herramienta fácil de usar para la búsqueda recursiva de subdominios, con detección de comodines. |
| **dnsrecon**    | Herramienta versátil que combina múltiples técnicas de reconocimiento DNS.                       |
| **amass**       | Herramienta de descubrimiento de subdominios con integración de datos y otras herramientas.      |
| **assetfinder** | Herramienta simple pero efectiva para encontrar subdominios mediante diversas técnicas.          |
| **puredns**     | Herramienta potente y flexible para fuerza bruta DNS, capaz de resolver y filtrar resultados.    |

## DNSenum

**dnsenum** es una herramienta de línea de comandos versátil y ampliamente utilizada, escrita en Perl. Es un kit de herramientas completo para el reconocimiento de DNS, con varias funcionalidades clave:

* **Enumeración de registros DNS:** dnsenum puede recuperar varios registros DNS como A, AAAA, NS, MX y TXT, proporcionando una vista completa de la configuración DNS del objetivo.
* **Intentos de transferencia de zona:** Intenta transferencias de zona automáticamente desde servidores de nombres descubiertos. Aunque la mayoría de los servidores están configurados para prevenir transferencias no autorizadas, un intento exitoso puede revelar mucha información.
* **Fuerza bruta de subdominios:** dnsenum admite la enumeración por fuerza bruta utilizando una lista de palabras para probar subdominios potenciales contra el dominio objetivo.
* **Scraping de Google:** Puede extraer resultados de Google para encontrar subdominios adicionales que podrían no estar en los registros DNS.
* **Búsqueda inversa:** dnsenum puede realizar búsquedas DNS inversas para identificar dominios asociados con una dirección IP, revelando potencialmente otros sitios alojados en el mismo servidor.
* **Consultas WHOIS:** También puede realizar consultas WHOIS para obtener detalles sobre la propiedad y el registro del dominio.

#### Ejemplo práctico de dnsenum

Veamos **dnsenum** en acción enumerando subdominios del dominio `inlanefreight.com`. En esta demostración, utilizaremos la lista de palabras **subdomains-top1million-5000.txt** de **SecLists**, que contiene los 5000 subdominios más comunes.

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

**En este comando:**

* `dnsenum --enum inlanefreight.com`: Especificamos el dominio objetivo que queremos enumerar junto con la opción `--enum` para ajustar la enumeración.
* `-f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`: Indicamos la ruta a la lista de palabras de SecLists que utilizaremos para la fuerza bruta. Ajusta la ruta si tu instalación de SecLists es diferente.
* `-r`: Esta opción habilita la fuerza bruta recursiva de subdominios, lo que significa que si dnsenum encuentra un subdominio, intentará enumerar subdominios de ese subdominio.

#### Ejemplo de Fuerza Bruta de Subdominios

```bash
sherlock28@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----

Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Fuerza bruta con /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]

done.
```
