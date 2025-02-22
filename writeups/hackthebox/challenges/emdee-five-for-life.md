---
description: '🔍 Dificultad: Fácil'
---

# Emdee five for life

Entramos en el host que nos proporcionan.&#x20;

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 16.55.24.png" alt=""><figcaption></figcaption></figure>

Encriptamos la cadena en MD5 tal como se nos pide.

```
echo -n "Mlji3Pbgj4zaofjOMDvu" | md5sum
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 16.59.04.png" alt=""><figcaption></figcaption></figure>

Pegamos el resultado.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 16.58.28.png" alt=""><figcaption></figcaption></figure>

¡Demasiado lento! Parece que necesitamos hacer un script para conseguir la flag.

1. **Defino la URL del reto** en `URL`, que apunta a un servidor en la dirección `http://83.136.249.46:56367`.
2. **Inicio una sesión con `requests`**, lo que me permite mantener cookies o autenticaciones necesarias.
3. **Hago una petición GET a la URL** para obtener el contenido de la página web.
4. **Uso BeautifulSoup para analizar el HTML** de la respuesta y buscar elementos `<h3>`. Normalmente, dentro de estos elementos está el texto que necesito procesar.
5. **Extraigo el texto dentro de `<h3>`** y lo guardo en la variable `md5`. Este texto parece ser un valor que debo transformar.
6. **Aplico la función hash MD5** sobre ese texto usando `hashlib.md5()`, y luego convierto el resultado en un formato hexadecimal (`hexdigest()`). Este hash será la respuesta esperada por el reto.
7. **Creo un diccionario con el hash MD5** (`data = {'hash': md5_hash}`), que será enviado en una petición POST al servidor.
8. **Envió el hash de vuelta al servidor** con `request.post(url=URL, data=data)`, esperando que me responda con algún mensaje, como “¡Correcto!” o “Flag: …”.
9. **Imprimo la respuesta del servidor**, lo que probablemente contenga la flag o algún otro mensaje de confirmación.

```
from bs4 import BeautifulSoup
import requests
import hashlib

URL = "http://83.136.249.46:56367"
request = requests.session()
page = request.get(URL)

soup = BeautifulSoup(page.text, "html.parser")

for i in soup.findAll('h3'):
	md5 = (i.get_text())
	#print(md5)

hash_object = hashlib.md5(md5.encode())
md5_hash = hash_object.hexdigest()
#print(md5_hash)

data = {'hash': md5_hash}
output = request.post(url = URL, data = data)

print(output.text)
```

Ejecutamos.

```
python3 prueba.py
```

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 17.06.48.png" alt=""><figcaption></figcaption></figure>
