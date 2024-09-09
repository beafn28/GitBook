# Cloud Resources

El uso de la nube, como AWS, GCP, Azure, y otros, es ahora uno de los componentes esenciales para muchas empresas hoy en día. Después de todo, todas las empresas quieren poder trabajar desde cualquier lugar, por lo que necesitan un punto central para toda la gestión. Es por eso que los servicios de Amazon (AWS), Google (GCP) y Microsoft (Azure) son ideales para este propósito.

Aunque los proveedores de la nube aseguran su infraestructura de forma centralizada, esto no significa que las empresas estén libres de vulnerabilidades. Las configuraciones realizadas por los administradores pueden, no obstante, hacer que los recursos en la nube de la empresa sean vulnerables. Esto a menudo comienza con los **S3 buckets** (AWS), **blobs** (Azure) y **cloud storage** (GCP), que pueden ser accedidos sin autenticación si están configurados incorrectamente.

### Servidores Alojados en la Empresa

```bash
sherlock28@htb[/htb]$ for i in $(cat subdomainlist); do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4; done
```

```bash
blog.inlanefreight.com 10.129.24.93  
inlanefreight.com 10.129.27.33  
matomo.inlanefreight.com 10.129.127.22  
www.inlanefreight.com 10.129.127.33  
s3-website-us-west-2.amazonaws.com 10.129.95.250  
```

A menudo se agrega el almacenamiento en la nube a la lista de DNS cuando es utilizado para propósitos administrativos por otros empleados. Este paso hace que sea mucho más fácil para los empleados acceder y gestionarlo. Mantengámonos en el caso de que una empresa nos ha contratado, y durante la búsqueda de IP ya hemos visto que una dirección IP pertenece al servidor **s3-website-us-west-2.amazonaws.com**.

Sin embargo, hay muchas formas diferentes de encontrar dicho almacenamiento en la nube. Una de las más fáciles y usadas es la búsqueda en Google combinada con **Google Dorks**. Por ejemplo, podemos usar los **Google Dorks** `inurl:` e `intext:` para limitar nuestra búsqueda a términos específicos. En el siguiente ejemplo, vemos áreas rojas censuradas que contienen el nombre de la empresa.

Aquí ya podemos ver que los enlaces presentados por Google contienen archivos **PDF**. Cuando buscamos una empresa que ya conocemos o queremos conocer, también nos encontraremos con otros archivos como documentos de texto, presentaciones, códigos y muchos otros.

Dicho contenido también suele estar incluido en el código fuente de las páginas web, desde donde se cargan imágenes, códigos JavaScript o CSS. Este procedimiento a menudo alivia el servidor web y no almacena contenido innecesario.

### Código Fuente del Sitio Web Objetivo

Los proveedores externos, como **domain.glass**, también pueden decirnos mucho sobre la infraestructura de la empresa. Como efecto secundario positivo, también podemos ver que el estado de evaluación de seguridad de **Cloudflare** ha sido clasificado como "Seguro". Esto significa que ya hemos encontrado una medida de seguridad que se puede anotar para la segunda capa (puerta de enlace).

### Resultados de Domain.Glass

Otro proveedor muy útil es **GrayHatWarfare**. Podemos hacer muchas búsquedas diferentes, descubrir almacenamiento en la nube de AWS, Azure y GCP, e incluso ordenar y filtrar por formato de archivo. Por lo tanto, una vez que los hayamos encontrado a través de Google, también podemos buscarlos en **GrayHatWarefare** y descubrir pasivamente qué archivos están almacenados en dicho almacenamiento en la nube.

### Resultados de GrayHatWarfare

Muchas empresas también usan abreviaciones del nombre de la empresa, que luego se utilizan en consecuencia dentro de la infraestructura de TI. Dichos términos también forman parte de un excelente enfoque para descubrir nuevo almacenamiento en la nube de la empresa. También podemos buscar archivos al mismo tiempo para ver los archivos que se pueden acceder simultáneamente.

### Claves SSH Privadas y Públicas Filtradas

A veces, cuando los empleados están sobrecargados de trabajo o bajo alta presión, los errores pueden ser fatales para toda la empresa. Estos errores pueden incluso llevar a que se filtren claves privadas de SSH, lo que cualquiera puede descargar e iniciar sesión en una o más máquinas de la empresa sin necesidad de usar una contraseña.
