# Password Reuse / Default Passwords

Es común que tanto los usuarios como los administradores dejen configuraciones predeterminadas sin cambiar. Los administradores tienen la responsabilidad de gestionar toda la tecnología, infraestructura y aplicaciones, junto con los datos que se acceden. En este contexto, es frecuente que se utilice la misma contraseña para fines de configuración, y luego se olvide cambiarla para una interfaz u otra. Además, muchas aplicaciones que trabajan con mecanismos de autenticación, prácticamente todas, a menudo vienen con credenciales predeterminadas tras la instalación. Estas credenciales predeterminadas pueden ser olvidadas y no se cambian después de la configuración, especialmente cuando se trata de aplicaciones internas donde los administradores asumen que nadie más las encontrará ni intentará usarlas.

Asimismo, es habitual reutilizar contraseñas fáciles de recordar que se pueden escribir rápidamente, en lugar de teclear contraseñas largas de 15 caracteres, ya que el inicio de sesión único (SSO, por sus siglas en inglés) no siempre está disponible inmediatamente durante la instalación inicial, y la configuración de redes internas requiere cambios significativos. Al configurar redes, a veces trabajamos con infraestructuras vastas (dependiendo del tamaño de la empresa) que pueden tener cientos de interfaces. Frecuentemente se pasa por alto un dispositivo de red, como un enrutador, impresora o firewall, y se utilizan credenciales predeterminadas o se reutiliza la misma contraseña.

## Ataques de Credential Stuffing

Existen varias bases de datos que mantienen listas de credenciales predeterminadas conocidas. Una de ellas es la **DefaultCreds-Cheat-Sheet**. A continuación, se muestra un pequeño extracto de la tabla completa de esta hoja de trucos:

| Producto/Vendedor | Usuario  | Contraseña                     |
| ----------------- | -------- | ------------------------------ |
| Zyxel (ssh)       | zyfwp    | PrOw!aN\_fXp                   |
| APC UPS (web)     | apc      | apc                            |
| Weblogic (web)    | system   | manager                        |
| Weblogic (web)    | weblogic | weblogic1                      |
| Weblogic (web)    | WEBLOGIC | WEBLOGIC                       |
| Weblogic (web)    | PUBLIC   | PUBLIC                         |
| Weblogic (web)    | EXAMPLES | EXAMPLES                       |
| Weblogic (web)    | system   | password                       |
| Weblogic (web)    | weblogic | welcome(1)                     |
| Weblogic (web)    | operator | weblogic                       |
| Kanboard (web)    | admin    | admin                          |
| Vectr (web)       | admin    | 11\_ThisIsTheFirstPassword\_11 |
| Caldera (web)     | admin    | admin                          |
| Dlink (web)       | admin    | admin                          |
| Dlink (web)       | 1234     | 1234                           |
| JioFiber          | admin    | jiocentrum                     |
| GigaFiber         | admin    | jiocentrum                     |
| Kali Linux (OS)   | kali     | kali                           |
| F5                | admin    | admin                          |
| F5                | root     | default                        |
| F5                | support  |                                |

Las credenciales predeterminadas también se pueden encontrar en la documentación del producto, ya que contienen los pasos necesarios para configurar el servicio con éxito. Algunos dispositivos o aplicaciones requieren que el usuario configure una contraseña durante la instalación, pero otros usan contraseñas predeterminadas débiles. Atacar esos servicios utilizando las credenciales predeterminadas u obtenidas se llama **Credential Stuffing**. Esta es una variante simplificada de un ataque de fuerza bruta, ya que solo se usan combinaciones de nombres de usuario y las contraseñas asociadas.

Es común que tanto los usuarios como los administradores dejen configuraciones predeterminadas sin cambiar. Los administradores tienen la responsabilidad de gestionar toda la tecnología, infraestructura y aplicaciones, junto con los datos que se acceden. En este contexto, es frecuente que se utilice la misma contraseña para fines de configuración, y luego se olvide cambiarla para una interfaz u otra. Además, muchas aplicaciones que trabajan con mecanismos de autenticación, prácticamente todas, a menudo vienen con credenciales predeterminadas tras la instalación. Estas credenciales predeterminadas pueden ser olvidadas y no se cambian después de la configuración, especialmente cuando se trata de aplicaciones internas donde los administradores asumen que nadie más las encontrará ni intentará usarlas.

Asimismo, es habitual reutilizar contraseñas fáciles de recordar que se pueden escribir rápidamente, en lugar de teclear contraseñas largas de 15 caracteres, ya que el inicio de sesión único (SSO, por sus siglas en inglés) no siempre está disponible inmediatamente durante la instalación inicial, y la configuración de redes internas requiere cambios significativos. Al configurar redes, a veces trabajamos con infraestructuras vastas (dependiendo del tamaño de la empresa) que pueden tener cientos de interfaces. Frecuentemente se pasa por alto un dispositivo de red, como un enrutador, impresora o firewall, y se utilizan credenciales predeterminadas o se reutiliza la misma contraseña.

#### Ataques de Credential Stuffing

Existen varias bases de datos que mantienen listas de credenciales predeterminadas conocidas. Una de ellas es la **DefaultCreds-Cheat-Sheet**. A continuación, se muestra un pequeño extracto de la tabla completa de esta hoja de trucos:

| Producto/Vendedor | Usuario  | Contraseña                     |
| ----------------- | -------- | ------------------------------ |
| Zyxel (ssh)       | zyfwp    | PrOw!aN\_fXp                   |
| APC UPS (web)     | apc      | apc                            |
| Weblogic (web)    | system   | manager                        |
| Weblogic (web)    | weblogic | weblogic1                      |
| Weblogic (web)    | WEBLOGIC | WEBLOGIC                       |
| Weblogic (web)    | PUBLIC   | PUBLIC                         |
| Weblogic (web)    | EXAMPLES | EXAMPLES                       |
| Weblogic (web)    | system   | password                       |
| Weblogic (web)    | weblogic | welcome(1)                     |
| Weblogic (web)    | operator | weblogic                       |
| Kanboard (web)    | admin    | admin                          |
| Vectr (web)       | admin    | 11\_ThisIsTheFirstPassword\_11 |
| Caldera (web)     | admin    | admin                          |
| Dlink (web)       | admin    | admin                          |
| Dlink (web)       | 1234     | 1234                           |
| JioFiber          | admin    | jiocentrum                     |
| GigaFiber         | admin    | jiocentrum                     |
| Kali Linux (OS)   | kali     | kali                           |
| F5                | admin    | admin                          |
| F5                | root     | default                        |
| F5                | support  |                                |

Las credenciales predeterminadas también se pueden encontrar en la documentación del producto, ya que contienen los pasos necesarios para configurar el servicio con éxito. Algunos dispositivos o aplicaciones requieren que el usuario configure una contraseña durante la instalación, pero otros usan contraseñas predeterminadas débiles. Atacar esos servicios utilizando las credenciales predeterminadas u obtenidas se llama **Credential Stuffing**. Esta es una variante simplificada de un ataque de fuerza bruta, ya que solo se usan combinaciones de nombres de usuario y las contraseñas asociadas.

### Uso de Hydra para Credential Stuffing

Imaginemos que hemos encontrado algunas aplicaciones utilizadas en la red de nuestros clientes. Después de buscar en Internet las credenciales predeterminadas, podemos crear una nueva lista que separe estas combinaciones de credenciales con dos puntos (usuario

ña). Además, podemos seleccionar las contraseñas y mutarlas según nuestras reglas para aumentar la probabilidad de aciertos.

```bash
sherlock28@htb[/htb]$ hydra -C <user_pass.list> <protocol>://<IP>
```

Ejemplo de uso:

```bash
sherlock28@htb[/htb]$ hydra -C user_pass.list ssh://10.129.42.197
```

Aquí, el **OSINT** juega otro papel importante. Debido a que el OSINT nos da una "sensación" de cómo está estructurada la empresa y su infraestructura, podremos entender qué contraseñas y nombres de usuario podemos combinar. Luego, podemos almacenarlos en nuestras listas y usarlos después. Además, podemos usar Google para verificar si las aplicaciones que encontramos tienen credenciales codificadas que se puedan utilizar.

### Búsqueda en Google - Credenciales predeterminadas

Además de las credenciales predeterminadas para aplicaciones, algunas listas también ofrecen credenciales predeterminadas para enrutadores. Una de estas listas se puede encontrar aquí. Es mucho menos probable que las credenciales predeterminadas de los enrutadores se dejen sin cambiar, ya que estos son las interfaces centrales para las redes, y los administradores generalmente prestan mucha más atención a fortalecerlos. Sin embargo, aún es posible que se pase por alto un enrutador o que esté siendo utilizado solo en la red interna con fines de prueba, lo cual podemos aprovechar para ataques posteriores.

<figure><img src="../../.gitbook/assets/Google-default-creds (1).webp" alt=""><figcaption></figcaption></figure>

| Marca de Enrutador | Dirección IP Predeterminada                  | Usuario Predeterminado | Contraseña Predeterminada |
| ------------------ | -------------------------------------------- | ---------------------- | ------------------------- |
| 3Com               | [http://192.168.1.1](http://192.168.1.1)     | admin                  | Admin                     |
| Belkin             | [http://192.168.2.1](http://192.168.2.1)     | admin                  | admin                     |
| BenQ               | [http://192.168.1.1](http://192.168.1.1)     | admin                  | Admin                     |
| D-Link             | [http://192.168.0.1](http://192.168.0.1)     | admin                  | Admin                     |
| Digicom            | [http://192.168.1.254](http://192.168.1.254) | admin                  | Michelangelo              |
| Linksys            | [http://192.168.1.1](http://192.168.1.1)     | admin                  | Admin                     |
| Netgear            | [http://192.168.0.1](http://192.168.0.1)     | admin                  | password                  |
| ...                | ...                                          | ...                    | ...                       |
