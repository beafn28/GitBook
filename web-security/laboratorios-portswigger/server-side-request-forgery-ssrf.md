# Server-side request forgery (SSRF)

## Lab: Basic SSRF against the local server

### Enunciado

Este laboratorio tiene una funcionalidad de **verificación de stock** que obtiene datos desde un **sistema interno**. **Modificar la URL usada en la verificación de stock para acceder a la interfaz de administración en `http://localhost/admin`** y **eliminar al usuario `carlos`**.

### Resolución

Entramos a un producto y comprobamos la petición.

<figure><img src="../../.gitbook/assets/image (1400).png" alt=""><figcaption></figcaption></figure>

Como el servidor web tiene esos permisos accedemos a la URL cambiandola ya que en el **/admin** nos decía que se podría entrar en localhost.

<figure><img src="../../.gitbook/assets/image (1401).png" alt=""><figcaption></figcaption></figure>

Ahora vemos la ruta para eliminar al usuario y hacemos lo mismo cambiando la URL para enviarlo y así se elimina.

<figure><img src="../../.gitbook/assets/image (1402).png" alt=""><figcaption></figcaption></figure>

## Lab: Basic SSRF against another back-end system

### Enunciado

Este laboratorio tiene una funcionalidad de **verificación de stock** que obtiene datos desde un **sistema interno**. Usar la funcionalidad de verificación de stock para **escanear el rango interno `192.168.0.X` en el puerto 8080**, localizar una **interfaz de administración**, y utilizarla para **eliminar al usuario `carlos`**.

### Resolución

No se encuentra **/admin** por lo que vamos a ver el stock de un producto y su petición.

<figure><img src="../../.gitbook/assets/image (1403).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1404).png" alt=""><figcaption></figcaption></figure>

Hacer peticiones a una IP eso está mal por lo que enviamos esa petición pero para la ruta **/admin**.

<figure><img src="../../.gitbook/assets/image (1405).png" alt=""><figcaption></figcaption></figure>

Nos da error por lo que no se encuentra en esa IP. Enviamos al **Intruder** y realizamos una fuerza bruta para ver que IP es.

<figure><img src="../../.gitbook/assets/image (1406).png" alt=""><figcaption></figcaption></figure>

Encontramos una solución que nos da código 200.

<figure><img src="../../.gitbook/assets/image (1407).png" alt=""><figcaption></figcaption></figure>

Con la petición del stock con la ruta de eliminar como hicimos antes pero con la IP correcta.

<figure><img src="../../.gitbook/assets/image (1408).png" alt=""><figcaption></figcaption></figure>

## Lab: Blind SSRF with out-of-band detection

### Enunciado

Este sitio utiliza un **software de análisis** que **obtiene (fetch)** la URL especificada en el **header Referer** cuando se carga una página de producto.

Para resolver el laboratorio, **aprovecha esta funcionalidad** para provocar que se realice una **solicitud HTTP** al servidor público de **Burp Collaborator**.

### Resolución

Interceptamos la petición.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-10 102048.png" alt=""><figcaption></figcaption></figure>

Cambiamos el header Referer por el enlace del Collaborator.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-10 102141.png" alt=""><figcaption></figcaption></figure>

Enviamos petición.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-10 102205.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-07-10 102226.png" alt=""><figcaption></figcaption></figure>

## Lab: SSRF with blacklist-based input filter

### Enunciado

Este laboratorio tiene una función de **verificación de stock** que obtiene datos de un **sistema interno**.

Para resolver el laboratorio, **modifica la URL de verificación de stock** para acceder a la interfaz de administración en:

```
http://localhost/admin
```

y **elimina al usuario carlos**.

El desarrollador ha implementado **dos defensas anti-SSRF débiles** que tendrás que **eludir**.

### Resolución

Miramos la petición de stock.

<figure><img src="../../.gitbook/assets/image (1571).png" alt=""><figcaption></figcaption></figure>

Enviamos el enlace del enunciado.

<figure><img src="../../.gitbook/assets/image (1572).png" alt=""><figcaption></figcaption></figure>

Vemos varias maneras de encodearlo y nos salión con la codificación doble de caracteres.

```
http://127.1/%2561dmin
```

<figure><img src="../../.gitbook/assets/image (1573).png" alt=""><figcaption></figcaption></figure>

Ya podemos eliminar al usuario Carlos.

<figure><img src="../../.gitbook/assets/image (1574).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1575).png" alt=""><figcaption></figcaption></figure>

## Lab: SSRF with filter bypass via open redirection vulnerability

### Enunciado

Este laboratorio tiene una función de **verificación de stock** que obtiene datos de un **sistema interno**.

Para resolver el laboratorio, **modifica la URL de verificación de stock** para acceder a la interfaz de administración en:

```
http://192.168.0.12:8080/admin
```

y **elimina al usuario carlos**.

El verificador de stock ha sido **restringido para acceder únicamente a la aplicación local**, por lo que primero **necesitarás encontrar un open redirect** que afecte a la aplicación.

### Resolución

Miramos la petición de stock.

<figure><img src="../../.gitbook/assets/image (1576).png" alt=""><figcaption></figcaption></figure>

No nos deja completar dominios ni subdominios.

Detectamos una **vulnerabilidad de redirección abierta** en el parámetro **path** del endpoint `nextProduct`.

Aprovechamos esta redirección para lograr que el **stock checker** accediera de forma indirecta al **panel de administración interno** en:

```
http://192.168.0.12:8080/admin
```

Una vez allí, aplicamos la misma técnica para **construir una URL** que permitiera **eliminar al usuario carlos**, logrando así completar el laboratorio.

<figure><img src="../../.gitbook/assets/image (1577).png" alt=""><figcaption></figcaption></figure>

Ya podemos eliminar al usuario.

<figure><img src="../../.gitbook/assets/image (1578).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1579).png" alt=""><figcaption></figcaption></figure>
