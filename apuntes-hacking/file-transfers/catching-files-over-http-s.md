# Catching Files over HTTP/S

## HTTP/S

La transferencia de archivos a través de la web es el método más común debido a que los protocolos **HTTP/HTTPS** son los más frecuentemente permitidos a través de firewalls. Un beneficio significativo es que, en muchos casos, el archivo estará cifrado durante el tránsito. En una prueba de penetración, no hay nada peor que una IDS de red del cliente detecte que se ha transferido un archivo sensible en texto plano y que te pregunten por qué enviamos una contraseña a nuestro servidor en la nube sin usar cifrado.

Ya hemos discutido el uso del módulo **uploadserver** de **Python3** para configurar un servidor web con capacidades de carga, pero también podemos usar **Apache** o **Nginx**. Esta sección cubrirá cómo crear un servidor web seguro para operaciones de carga de archivos.

## Nginx - Habilitando PUT

Una buena alternativa a **Apache** para transferir archivos es **Nginx**, ya que su configuración es menos complicada y el sistema de módulos no conduce a problemas de seguridad como puede ocurrir con Apache.

Cuando se permiten cargas HTTP, es crítico asegurarse al 100% de que los usuarios no puedan subir **web shells** y ejecutarlos. Apache facilita cometer este error, ya que el módulo PHP ejecuta cualquier cosa que termine en PHP. Configurar Nginx para usar PHP no es tan sencillo.

### **Crear un Directorio para Manejar Archivos Subidos**

```bash
sherlock28@htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

### **Cambiar el Propietario a www-data**

```bash
sherlock28@htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

### **Crear Archivo de Configuración para Nginx**

Crear el archivo de configuración de Nginx en `/etc/nginx/sites-available/upload.conf` con el siguiente contenido:

```bash
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

### **Crear un Enlace Simbólico a sites-enabled**

```bash
sherlock28@htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

### **Iniciar Nginx**

```bash
sherlock28@htb[/htb]$ sudo systemctl restart nginx.service
```

Si recibimos mensajes de error, debemos revisar los registros en `/var/log/nginx/error.log`. Si estamos usando **Pwnbox**, veremos que el puerto 80 ya está en uso.

### **Verificación de Errores**

```bash
sherlock28@htb[/htb]$ tail -2 /var/log/nginx/error.log

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:80 failed (98: Address already in use)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```

Verificar qué servicio está utilizando el puerto 80:

```bash
sherlock28@htb[/htb]$ ss -lnpt | grep 80

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=2811,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))
```

Verificar el proceso:

```bash
sherlock28@htb[/htb]$ ps -ef | grep 2811

user65      2811    1856  0 16:05 ?        00:00:04 python -m websockify 80 localhost:5901 -D
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811
```

Vemos que ya hay un módulo utilizando el puerto 80. Para solucionarlo, podemos eliminar la configuración predeterminada de Nginx que vincula el puerto 80.

### **Eliminar la Configuración Predeterminada de Nginx**

```bash
sherlock28@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Ahora podemos probar la carga de archivos utilizando **cURL** para enviar una solicitud **PUT**. En el siguiente ejemplo, subiremos el archivo `/etc/passwd` al servidor y lo llamaremos `users.txt`.

### **Subir un Archivo Usando cURL**

```bash
sherlock28@htb[/htb]$ curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```

Verificar la subida del archivo:

```bash
sherlock28@htb[/htb]$ sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt

user65:x:1000:1000:,,,:/home/user65:/bin/bash
```

***

Una vez que esto esté funcionando, una buena prueba es asegurarse de que la lista de directorios no esté habilitada navegando a [**http://localhost/SecretUploadDirectory**](http://localhost/SecretUploadDirectory). De forma predeterminada, con **Apache**, si accedemos a un directorio sin un archivo **index** (index.html), listará todos los archivos. Esto es peligroso cuando estamos exfiltrando archivos sensibles, ya que queremos ocultarlos lo mejor posible. Afortunadamente, Nginx no habilita esta función de manera predeterminada.

## Uso de Herramientas Incorporadas

En la siguiente sección, introduciremos el tema de "Living off the Land", o el uso de herramientas incorporadas en Windows y Linux para realizar actividades de transferencia de archivos. Volveremos a este concepto en varios módulos del camino de Penetration Tester, cuando cubramos tareas como la escalada de privilegios en Windows y Linux, y la enumeración y explotación de **Active Directory**.
