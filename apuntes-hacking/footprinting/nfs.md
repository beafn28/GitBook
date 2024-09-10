# NFS

NFS es un sistema de archivos de red desarrollado por Sun Microsystems con el mismo propósito que SMB: acceder a sistemas de archivos a través de una red como si fueran locales. Sin embargo, NFS utiliza un protocolo completamente diferente. NFS es utilizado entre sistemas Linux y Unix, lo que significa que los clientes NFS no pueden comunicarse directamente con servidores SMB. NFS es un estándar de Internet que regula los procedimientos en un sistema de archivos distribuido.

Si bien la versión 3.0 del protocolo NFS (NFSv3), que ha estado en uso durante muchos años, autentica la computadora cliente, esto cambia con NFSv4, donde, al igual que con el protocolo SMB de Windows, el usuario debe autenticarse.

| Versión   | Características                                                                                                                                                                                                                                              |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **NFSv2** | Es más antigua pero es compatible con muchos sistemas, y al principio funcionaba completamente sobre UDP.                                                                                                                                                    |
| **NFSv3** | Ofrece más características, como tamaño de archivo variable y mejor reporte de errores, pero no es totalmente compatible con clientes NFSv2.                                                                                                                 |
| **NFSv4** | Incluye Kerberos, funciona a través de cortafuegos y en Internet, ya no requiere portmappers, soporta ACLs, aplica operaciones basadas en estado, y ofrece mejoras de rendimiento y alta seguridad. Es la primera versión con un protocolo basado en estado. |

La versión 4.1 de NFS (RFC 8881) busca proporcionar soporte de protocolo para aprovechar implementaciones de servidores en clústeres, incluyendo la capacidad de ofrecer acceso paralelo escalable a archivos distribuidos en varios servidores (extensión pNFS). Además, NFSv4.1 incluye un mecanismo de _session trunking_, también conocido como NFS multipath. Una ventaja importante de NFSv4 sobre sus predecesores es que solo usa un puerto UDP o TCP, el 2049, para ejecutar el servicio, lo que simplifica el uso del protocolo a través de cortafuegos.

NFS se basa en el protocolo Open Network Computing Remote Procedure Call (ONC-RPC/SUN-RPC) expuesto en los puertos TCP y UDP 111, utilizando External Data Representation (XDR) para el intercambio de datos independiente del sistema. El protocolo NFS no tiene un mecanismo para autenticación o autorización. En su lugar, la autenticación se delega completamente a las opciones del protocolo RPC. La autorización se deriva de la información disponible en el sistema de archivos. En este proceso, el servidor es responsable de traducir la información del usuario del cliente al formato del sistema de archivos y convertir los detalles de autorización correspondientes al formato UNIX requerido lo más exactamente posible.

#### Autenticación más común en NFS

La autenticación más común es a través de UID/GID de UNIX y las membresías de grupo, que es el formato que más probablemente se aplique al protocolo NFS. Un problema es que el cliente y el servidor no necesariamente deben tener las mismas asignaciones de UID/GID para usuarios y grupos, y el servidor no necesita hacer nada más. No se pueden realizar más verificaciones por parte del servidor, por lo que este método de autenticación solo debe usarse en redes confiables.

## Configuración predeterminada

NFS no es difícil de configurar, ya que no tiene tantas opciones como FTP o SMB. El archivo `/etc/exports` contiene una tabla de sistemas de archivos físicos en un servidor NFS que son accesibles por los clientes. La tabla de exportaciones NFS muestra qué opciones acepta y, por lo tanto, indica qué opciones están disponibles para nosotros.

### **Archivo `/etc/exports`**

```bash
sherlock28@htb[/htb]$ cat /etc/exports 

# /etc/exports: la lista de control de acceso para los sistemas de archivos que pueden ser exportados
#               a clientes NFS.  Ver exports(5).
#
# Ejemplo para NFSv2 y NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Ejemplo para NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
```

El archivo de exportaciones predeterminado también contiene algunos ejemplos de configuración de NFS shares. Primero, se especifica la carpeta que se hará disponible a otros, y luego se conectan los derechos que tendrán en este NFS share a un host o una subred. Finalmente, se pueden agregar opciones adicionales a los hosts o subredes.

| Opción                 | Descripción                                                                                                                                |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **rw**                 | Permisos de lectura y escritura.                                                                                                           |
| **ro**                 | Permisos de solo lectura.                                                                                                                  |
| **sync**               | Transferencia de datos síncrona. (Un poco más lenta)                                                                                       |
| **async**              | Transferencia de datos asíncrona. (Un poco más rápida)                                                                                     |
| **secure**             | No se usarán puertos por encima de 1024.                                                                                                   |
| **insecure**           | Se usarán puertos por encima de 1024.                                                                                                      |
| **no\_subtree\_check** | Esta opción desactiva la verificación de subdirectorios.                                                                                   |
| **root\_squash**       | Asigna todos los permisos de archivos del UID/GID root (0) al UID/GID anónimo, lo que impide que root acceda a archivos en un montaje NFS. |

Vamos a crear una entrada de prueba y jugar con la configuración.

### ExportFS en NFS

```bash
root@nfs:~# echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
root@nfs:~# systemctl restart nfs-kernel-server 
root@nfs:~# exportfs

/mnt/nfs      	10.129.14.0/24
```

Hemos compartido la carpeta `/mnt/nfs` con la subred `10.129.14.0/24` con la configuración que se muestra arriba. Esto significa que todos los hosts en la red podrán montar este NFS share e inspeccionar el contenido de esta carpeta.

