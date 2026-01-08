# JWT

Javascript Web Token (JWT) es una forma segura de transmitir información entre partes como un objeto JSON. Comprende tres partes: **header,** **payload** y **signature**. El header contiene información sobre el tipo de token y el algoritmo de hash utilizado para firmarlo. El paylaod contiene los datos reales que se están transmitiendo, tal como la identidad del usuario o un ID de sesión. La firma se utiliza para verificar la autenticidad del token.

<figure><img src="../../.gitbook/assets/image (1722).png" alt=""><figcaption></figcaption></figure>

* Hay que verificar si el servidor verifica la firma para ello editamos el subvalor y añadir administrador. Tras esto cambiamos el nuevo token creado.
*
