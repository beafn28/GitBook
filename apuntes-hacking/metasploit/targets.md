# Targets

Los objetivos son identificadores únicos de sistemas operativos tomados de las versiones específicas de esos sistemas operativos, que adaptan el módulo de exploit seleccionado para ejecutarse en esa versión en particular. El comando `show targets`, emitido dentro de la vista de un módulo de exploit, mostrará todos los objetivos vulnerables disponibles para ese exploit específico. Emitir el mismo comando en el menú principal, fuera de cualquier módulo de exploit seleccionado, nos indicará que primero necesitamos seleccionar un módulo de exploit.

## MSF - Mostrar Objetivos

```sql
msf6 > show targets
[-] No exploit module selected.
```

Al ver el módulo de exploit anterior, esto es lo que veríamos:

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s)
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to be used on target for pretty listing
   SHARE                 ADMIN$                                   yes       The share to connect to
   SMBUser                                                        no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique
   LHOST                      yes       The listen address
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

## Selección de un Objetivo

Podemos ver que solo hay un tipo de objetivo general configurado para este tipo de exploit. ¿Qué sucede si cambiamos el módulo de exploit a algo que necesita rangos de objetivos más específicos? El siguiente exploit está dirigido a:

**MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability.**

Para obtener más información sobre este módulo y la vulnerabilidad que explota, podemos usar el comando `info`. Este comando es útil cuando no estamos seguros sobre los orígenes o la funcionalidad de los diferentes módulos de exploits o auxiliares.

### MSF - Selección de Objetivos

**Comando: `info`**

```yaml
msf6 exploit(windows/browser/ie_execcommand_uaf) > info

       Name: MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability 
     Module: exploit/windows/browser/ie_execcommand_uaf
   Platform: Windows
       Arch: 
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Good
  Disclosed: 2012-09-14

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   IE 7 on Windows XP SP3
  2   IE 8 on Windows XP SP3
  3   IE 7 on Windows Vista
  4   IE 8 on Windows Vista
  5   IE 8 on Windows 7
  6   IE 9 on Windows 7

References:
  https://cvedetails.com/cve/CVE-2012-4969/
```

Podemos ver opciones para diferentes versiones de Internet Explorer y varias versiones de Windows. Dejar la selección en `Automatic` permitirá que `msfconsole` realice la detección de servicios en el objetivo antes de lanzar un ataque exitoso.

**Comando: `set target <index no.>`**

```arduino
msf6 exploit(windows/browser/ie_execcommand_uaf) > set target 6
target => 6
```

## Tipos de Objetivos

Cada objetivo puede variar por paquete de servicio, versión del sistema operativo e incluso la versión del idioma. Todo depende de la dirección de retorno y otros parámetros en el objetivo o dentro del módulo de exploit. Para identificar correctamente un objetivo, necesitamos:

1. Obtener una copia de los binarios del objetivo.
2. Usar `msfpescan` para localizar una dirección de retorno adecuada.

Más adelante, exploraremos el desarrollo de exploits, la generación de payloads y la identificación de objetivos.
