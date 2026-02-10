# Insecure deserialization

## Lab 1: Modificación de objetos serializados

1. La aplicación utiliza una cookie de sesión que contiene un objeto PHP serializado, codificado en Base64 y URL, el cual define atributos de control como el estado administrativo del usuario.
2. Al decodificar el objeto, se observa el atributo admin configurado como b:0. Al modificarlo a b:1 y reenviar la petición, el servidor confía ciegamente en el objeto modificado, otorgando privilegios de administrador.
3. Con acceso al panel de administración, es posible realizar acciones privilegiadas, como eliminar al usuario objetivo (carlos), demostrando cómo una validación deficiente de objetos serializados puede derivar en una escalada de privilegios crítica.

## Lab 2: Modificación de tipos de datos serializados

1. La aplicación almacena la sesión en una cookie que contiene un objeto PHP serializado. Al interceptarla, se identifica un campo access\_token y el nombre de usuario, ambos utilizados para validar la identidad del usuario.
2. Se modifica el tipo de dato de access\_token, cambiándolo de cadena a entero (i:0), y se reemplaza el nombre de usuario por administrator, ajustando correctamente la longitud del valor serializado para mantener la coherencia del objeto.
3. El servidor acepta el objeto manipulado y interpreta la sesión como administrativa, permitiendo el acceso al panel de control y la eliminación del usuario carlos, evidenciando una grave falta de validación de tipos en la deserialización.

## Lab 3: Explotación vía funciones de la aplicación

1. La aplicación almacena en la cookie de sesión un objeto PHP serializado que incluye, entre otros atributos, la ruta del archivo de avatar del usuario (avatar\_link).
2. Aprovechando la funcionalidad legítima de eliminar cuenta, se modifica el valor de avatar\_link para que apunte a /home/carlos/morale.txt, manteniendo la estructura válida del objeto serializado.
3. Al eliminar la cuenta, el servidor confía en el valor proporcionado por el objeto deserializado y borra el archivo indicado, eliminando así morale.txt en lugar del avatar y resolviendo el laboratorio.

## Lab 4: Inyección de objetos arbitrarios en PHP

1. Al analizar el código fuente, se detecta una clase PHP que implementa el método mágico \_\_destruct(), el cual elimina el archivo especificado en el atributo lock\_file\_path cuando el objeto es destruido.
2. Se crea manualmente un objeto PHP serializado en el que lock\_file\_path apunta a /home/carlos/morale.txt. Este objeto se inyecta directamente en la cookie de sesión para forzar su deserialización por el servidor.
3. Al procesarse la petición, el objeto se deserializa y el método \_\_destruct() se ejecuta de forma implícita, provocando el borrado del archivo objetivo y completando el laboratorio.

