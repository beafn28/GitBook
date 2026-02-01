# Web Sockets

## Lab 1: Manipulación de mensajes WebSocket

1. Interceptar el mensaje y modificarlo manualmente antes de que llegue al servidor, conseguimos insertar una carga que, al ser procesada por el navegador del agente de soporte, genera una ejecución no deseada.
2. ```
   <img src="abc" onerror="alert(1)">
   ```
3. Enviamos.

## Lab 2: Secuestro de WebSocket entre sitios

1. Interceptamos los mensajes.
2. Mandamos este script al exploit server.\
   ![](<../../.gitbook/assets/image (3).png>)
3. Nos damos cuenta que se nos proporcionan unas cadenas base64 en la que pillamos una conversación.
4. Decodificamos.

## Lab 3: Manipulación del handshake de WebSocket

1. Creamos esta regla para que no nos bloqueen por IP.\
   ![](<../../.gitbook/assets/image (1) (1).png>)
2. Mandamos el siguiente payload.\
   ![](<../../.gitbook/assets/image (2) (1).png>)
