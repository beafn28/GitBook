# Well-Known URIs

**El estándar .well-known**, definido en el RFC 8615, actúa como un directorio estandarizado dentro del dominio raíz de un sitio web. Esta ubicación designada, típicamente accesible a través de la ruta `/.well-known/` en un servidor web, centraliza los metadatos críticos de un sitio web, incluidos archivos de configuración e información relacionada con sus servicios, protocolos y mecanismos de seguridad.

Al establecer una ubicación consistente para estos datos, .well-known simplifica el proceso de descubrimiento y acceso para diversas partes interesadas, incluidos navegadores web, aplicaciones y herramientas de seguridad. Este enfoque simplificado permite a los clientes localizar y recuperar automáticamente archivos de configuración específicos construyendo la URL adecuada. Por ejemplo, para acceder a la política de seguridad de un sitio web, un cliente solicitaría `https://example.com/.well-known/security.txt`.

La Autoridad de Números Asignados de Internet (IANA) mantiene un registro de URIs .well-known, cada uno con un propósito específico definido por diversas especificaciones y estándares. A continuación se muestra una tabla que destaca algunos ejemplos notables:

| Sufijo URI                   | Descripción                                                                                                                | Estado      | Referencia                                                                                     |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ----------- | ---------------------------------------------------------------------------------------------- |
| security.txt                 | Contiene información de contacto para que los investigadores de seguridad informen sobre vulnerabilidades.                 | Permanente  | RFC 9116                                                                                       |
| /.well-known/change-password | Proporciona una URL estándar para dirigir a los usuarios a una página de cambio de contraseña.                             | Provisional | [W3C](https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri) |
| openid-configuration         | Define detalles de configuración para OpenID Connect, una capa de identidad sobre el protocolo OAuth 2.0.                  | Permanente  | [OpenID Connect](http://openid.net/specs/openid-connect-discovery-1\_0.html)                   |
| assetlinks.json              | Se utiliza para verificar la propiedad de activos digitales (por ejemplo, aplicaciones) asociados con un dominio.          | Permanente  | [Google](https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md)  |
| mta-sts.txt                  | Especifica la política para SMTP MTA Strict Transport Security (MTA-STS) para mejorar la seguridad del correo electrónico. | Permanente  | RFC 8461                                                                                       |

Este es solo un pequeño ejemplo de los muchos URIs .well-known registrados en IANA. Cada entrada en el registro ofrece directrices y requisitos específicos para su implementación, asegurando un enfoque estandarizado para aprovechar el mecanismo .well-known para diversas aplicaciones.

## **Reconocimiento Web y .well-known**

En el reconocimiento web, los URIs .well-known pueden ser invaluables para descubrir puntos finales y detalles de configuración que pueden ser probados durante una prueba de penetración. Un URI particularmente útil es `openid-configuration`.

El URI `openid-configuration` es parte del protocolo de Descubrimiento OpenID Connect, una capa de identidad construida sobre el protocolo OAuth 2.0. Cuando una aplicación cliente desea utilizar OpenID Connect para la autenticación, puede recuperar la configuración del Proveedor OpenID Connect accediendo al punto final `https://example.com/.well-known/openid-configuration`. Este punto final devuelve un documento JSON que contiene metadatos sobre los puntos finales del proveedor, métodos de autenticación soportados, emisión de tokens y más:

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```

La información obtenida del punto final `openid-configuration` ofrece múltiples oportunidades de exploración:

* **Descubrimiento de Puntos Finales:**
  * **Punto Final de Autorización:** Identificar la URL para solicitudes de autorización de usuarios.
  * **Punto Final de Tokens:** Encontrar la URL donde se emiten los tokens.
  * **Punto Final de Información del Usuario:** Ubicar el punto final que proporciona información sobre el usuario.
* **JWKS URI:** El `jwks_uri` revela el Conjunto de Claves JSON Web (JWKS), detallando las claves criptográficas utilizadas por el servidor.
* **Alcances y Tipos de Respuesta Soportados:** Comprender qué alcances y tipos de respuesta son compatibles ayuda a mapear la funcionalidad y limitaciones de la implementación de OpenID Connect.
* **Detalles de Algoritmo:** La información sobre los algoritmos de firma soportados puede ser crucial para comprender las medidas de seguridad en su lugar.

Explorar el Registro de IANA y experimentar con los diversos URIs .well-known es un enfoque invaluable para descubrir oportunidades adicionales de reconocimiento web. Como se demostró con el punto final `openid-configuration` arriba, estos URIs estandarizados proporcionan acceso estructurado a metadatos críticos y detalles de configuración, permitiendo a los profesionales de seguridad mapear de manera integral el paisaje de seguridad de un sitio web.
