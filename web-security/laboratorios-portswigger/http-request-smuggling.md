# HTTP request smuggling

## Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses

### Enunciado

Este laboratorio tiene un escenario con un servidor front-end y uno back-end. El servidor front-end **no admite codificación chunked**.

Para resolver el laboratorio, debes **realizar un ataque de request smuggling** (inyección de petición encubierta) al servidor back-end, de forma que **la siguiente petición para la raíz (/) genere una respuesta 404 Not Found**.

> Aunque el laboratorio admite HTTP/2, la solución prevista requiere técnicas **solo posibles en HTTP/1**. Puedes cambiar manualmente de protocolo en **Burp Repeater**, en la sección _Request attributes_ del panel _Inspector_.

**Sugerencia**\
Ajustar manualmente los campos de longitud en ataques de request smuggling puede resultar complicado. Para facilitar este trabajo, Burp ofrece la extensión **HTTP Request Smuggler**, disponible en el **BApp Store**.

### Resolución

