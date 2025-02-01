# Path Traversal

### ¿Qué es Path Traversal?

Path Traversal, también conocido como Directory Traversal, es una vulnerabilidad que permite a un atacante leer archivos arbitrarios en el servidor donde se ejecuta una aplicación. Estos archivos pueden incluir:

* Código y datos de la aplicación.
* Credenciales de sistemas backend.
* Archivos sensibles del sistema operativo.

En algunos casos, el atacante puede escribir archivos en ubicaciones arbitrarias, lo que puede llevar al compromiso total del servidor.

### Cómo leer archivos arbitrarios mediante Path Traversal

#### Ejemplo:

Una aplicación de compras muestra imágenes de productos mediante una URL como esta:

```html
<img src="/loadImage?filename=218.png">
```

El servidor carga la imagen concatenando la entrada del usuario a un directorio base:

```
/var/www/images/218.png
```

Si no hay protección contra Path Traversal, un atacante puede manipular el parámetro `filename` para acceder a archivos sensibles, por ejemplo:

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

Esto permite acceder a:

```
/etc/passwd
```

En sistemas Windows, los atacantes pueden utilizar `..\..\..\\` en lugar de `../../../`, por ejemplo:

```
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```

### Obstáculos comunes y técnicas de evasión

#### 1. **Secuencias de traversal bloqueadas**

Algunas aplicaciones intentan eliminar `../` de la entrada del usuario. Esto puede evadirse con:

* **Uso de rutas absolutas:** `filename=/etc/passwd`
* **Secuencias anidadas:** `....//` o `....\/`
* **Codificación en URL:** `%2e%2e%2f` (doble codificación: `%252e%252e%252f`)
* **Codificación no estándar:** `..%c0%af` o `..%ef%bc%8f`

#### 2. **Validación del inicio del path**

Si la aplicación exige que el archivo empiece con un directorio base (`/var/www/images`), se puede intentar:

```
filename=/var/www/images/../../../etc/passwd
```

#### 3. **Validación de la extensión del archivo**

Si se exige una extensión específica (`.png`), se puede usar un **null byte** (`%00`):

```
filename=../../../etc/passwd%00.png
```

### ¿Cómo prevenir un ataque de Path Traversal?

1. **Evitar que la entrada del usuario se pase directamente a APIs del sistema de archivos.**
   * En lugar de permitir nombres de archivo arbitrarios, usar identificadores internos.
2. **Validar la entrada del usuario:**
   * Usar una **lista blanca** de nombres de archivo permitidos.
   * Asegurar que solo contenga caracteres alfanuméricos y seguros.
3. **Canonicalizar y verificar rutas antes de usarlas:**
   * Concatenar el directorio base y la entrada del usuario.
   * Usar una API de sistema para obtener la **ruta absoluta** y verificar que empiece con el directorio base.

#### Ejemplo de código en Java para prevenir Path Traversal:

```java
File file = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // Procesar archivo
}
```
