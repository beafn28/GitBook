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

## Configuraciones Peligrosas en NFS

Sin embargo, incluso con NFS, algunas configuraciones pueden ser peligrosas para la empresa y su infraestructura. Aquí se enumeran algunas de ellas:

| Opción               | Descripción                                                                                                                                     |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **rw**               | Permisos de lectura y escritura.                                                                                                                |
| **insecure**         | Se utilizarán puertos por encima de 1024.                                                                                                       |
| **nohide**           | Si otro sistema de archivos está montado debajo de un directorio exportado, este directorio se exporta por su propia entrada en `/etc/exports`. |
| **no\_root\_squash** | Todos los archivos creados por root se mantienen con el UID/GID 0.                                                                              |

Se recomienda encarecidamente crear una máquina virtual local y experimentar con las configuraciones. Descubriremos métodos que nos mostrarán cómo está configurado el servidor NFS. Para esto, podemos crear varias carpetas y asignar diferentes opciones a cada una. Luego, podemos inspeccionarlas y ver qué efectos tienen en el NFS share, sus permisos y el proceso de enumeración.

Podemos observar la opción **insecure**. Esta es peligrosa porque permite a los usuarios utilizar puertos por encima de 1024. Los primeros 1024 puertos solo pueden ser utilizados por root, lo que evita que usuarios normales usen sockets sobre el puerto 1024 para el servicio NFS e interactúen con él.

## Identificación del Servicio (Footprinting) NFS

Al realizar footprinting de NFS, los puertos TCP 111 y 2049 son esenciales. También podemos obtener información sobre el servicio NFS y el host a través de RPC, como se muestra en el siguiente ejemplo.

### **Nmap**

```bash
sherlock28@htb[/htb]$ sudo nmap 10.129.14.128 -p111,2049 -sV -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:12 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00018s latency).

PORT    STATE SERVICE VERSION
111/tcp open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)
```

El script `rpcinfo` de Nmap muestra una lista de todos los servicios RPC actualmente en ejecución, sus nombres, descripciones y los puertos que utilizan. Esto nos permite verificar si el _share_ objetivo está conectado a la red en todos los puertos necesarios.

Además, Nmap tiene algunos scripts NSE específicos para NFS que pueden utilizarse para los escaneos. Estos scripts pueden, por ejemplo, mostrar el contenido del _share_ y sus estadísticas.

```bash
sherlock28@htb[/htb]$ sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:37 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE VERSION
111/tcp  open  rpcbind 2-4 (RPC #100000)
| nfs-ls: Volume /mnt/nfs
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID    GID    SIZE  TIME                 FILENAME
| rwxrwxrwx   65534  65534  4096  2021-09-19T15:28:17  .
| ??????????  ?      ?      ?     ?                    ..
| rw-r--r--   0      0      1872  2021-09-19T15:27:42  id_rsa
| rw-r--r--   0      0      348   2021-09-19T15:28:17  id_rsa.pub
| rw-r--r--   0      0      0     2021-09-19T15:22:30  nfs.share
|_
| nfs-showmount: 
|_  /mnt/nfs 10.129.14.0/24
| nfs-statfs: 
|   Filesystem  1K-blocks   Used       Available   Use%  Maxfilesize  Maxlink
|_  /mnt/nfs    30313412.0  8074868.0  20675664.0  29%   16.0T        32000
```

Una vez que hayamos descubierto un servicio NFS, podemos montarlo en nuestra máquina local. Para ello, creamos una carpeta vacía a la que se montará el _share_ NFS. Una vez montado, podremos navegar por el _share_ y ver el contenido como si fuera parte de nuestro sistema local.

### Mostrar NFS Shares Disponibles

```bash
sherlock28@htb[/htb]$ showmount -e 10.129.14.128

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

### Montar el NFS Share

```bash
sherlock28@htb[/htb]$ mkdir target-NFS
sherlock28@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
sherlock28@htb[/htb]$ cd target-NFS
sherlock28@htb[/htb]$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```

Aquí tendremos la oportunidad de acceder a los permisos, nombres de usuarios y grupos a los que pertenecen los archivos mostrados. Una vez que tengamos los nombres de usuario, nombres de grupos, UIDs y GIDs, podemos crearlos en nuestro sistema y adaptarlos al _share_ NFS para ver y modificar los archivos.

### Listar Contenido con Nombres de Usuario y Grupos

```bash
sherlock28@htb[/htb]$ ls -l mnt/nfs/

total 16
-rw-r--r-- 1 cry0l1t3 cry0l1t3 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 cry0l1t3 cry0l1t3  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1 root     root     1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1 root     root      348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1 root     root        0 Sep 19 17:22 nfs.share
```

### Listar Contenido con UIDs y GIDs

```bash
sherlock28@htb[/htb]$ ls -n mnt/nfs/

total 16
-rw-r--r-- 1 1000 1000 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 1000 1000  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1    0 1000 1221 Sep 19 18:21 backup.sh
-rw-r--r-- 1    0    0 1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1    0    0  348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1    0    0    0 Sep 19 17:22 nfs.share
```

Es importante tener en cuenta que si la opción `root_squash` está configurada, no podremos editar el archivo `backup.sh`, incluso como root.

NFS puede usarse para una mayor escalada. Por ejemplo, si tenemos acceso al sistema a través de SSH y queremos leer archivos de otra carpeta a la que un usuario específico tiene acceso, podríamos subir una shell al _share_ NFS con el SUID de ese usuario y luego ejecutar la shell a través del usuario de SSH.

Después de realizar todos los pasos necesarios y obtener la información deseada, podemos desmontar el _share_ NFS.

### Desmontar el NFS

```bash
sherlock28@htb[/htb]$ cd ..
sherlock28@htb[/htb]$ sudo umount ./target-NFS
```
