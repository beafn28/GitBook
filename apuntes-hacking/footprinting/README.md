# Footprinting

{% file src="../../.gitbook/assets/Footprinting_Module_Cheat_Sheet.pdf" %}

## Metodología de Enumeración en Pruebas de Penetración

Los procesos complejos deben seguir una metodología estandarizada que nos ayude a mantener el rumbo y evitar omitir aspectos importantes. Especialmente debido a la variedad de casos que los sistemas objetivo pueden ofrecer, es casi impredecible cómo debe diseñarse nuestro enfoque. Por lo tanto, la mayoría de los probadores de penetración siguen sus hábitos y los pasos con los que se sienten más cómodos y familiarizados. Sin embargo, este no es un enfoque estandarizado, sino una aproximación basada en la experiencia.

Sabemos que las pruebas de penetración, y por lo tanto la enumeración, son procesos dinámicos. En consecuencia, hemos desarrollado una metodología de enumeración estática para pruebas de penetración externas e internas que incluye una dinámica flexible y permite una amplia gama de cambios y adaptaciones al entorno dado. Esta metodología está dividida en 6 capas que representan, metafóricamente hablando, fronteras que intentamos cruzar durante el proceso de enumeración. El proceso de enumeración se divide en tres niveles:

* Enumeración basada en infraestructura
* Enumeración basada en host
* Enumeración basada en sistema operativo

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> **Nota**: Los componentes de cada capa mostrada representan las principales categorías y no una lista completa de todos los componentes a buscar. Además, es importante mencionar que las dos primeras capas (Presencia en Internet y Gateway) no se aplican totalmente al entorno de una intranet, como una infraestructura de Active Directory. Las capas para la infraestructura interna se cubrirán en otros módulos.

### **Capas de Enumeración**

| Capa                                   | Descripción                                                                                                           | Categorías de Información                                                                                                                 |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 1. Presencia en Internet               | Identificación de la presencia en internet y la infraestructura accesible externamente.                               | Dominios, Subdominios, vHosts, ASN, Bloques de red, Direcciones IP, Instancias en la nube, Medidas de seguridad                           |
| 2. Gateway                             | Identificación de las posibles medidas de seguridad para proteger la infraestructura externa e interna de la empresa. | Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Segmentación de red, VPN, Cloudflare                                                          |
| 3. Servicios Accesibles                | Identificación de interfaces y servicios accesibles que se alojan externamente o internamente.                        | Tipo de servicio, Funcionalidad, Configuración, Puerto, Versión, Interfaz                                                                 |
| 4. Procesos                            | Identificación de los procesos internos, fuentes y destinos asociados con los servicios.                              | PID, Datos procesados, Tareas, Origen, Destino                                                                                            |
| 5. Privilegios                         | Identificación de permisos y privilegios internos a los servicios accesibles.                                         | Grupos, Usuarios, Permisos, Restricciones, Entorno                                                                                        |
| 6. Configuración del Sistema Operativo | Identificación de los componentes internos y la configuración del sistema.                                            | Tipo de sistema operativo, Nivel de parches, Configuración de red, Entorno del SO, Archivos de configuración, archivos privados sensibles |

#### Metodología en Práctica

Imaginemos que todo el proceso de pruebas de penetración es un laberinto donde tenemos que identificar los huecos y encontrar el camino para ingresar de manera rápida y efectiva. Este tipo de laberinto podría verse como sigue:

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> Los cuadrados representan los huecos o vulnerabilidades.

Un hecho interesante y muy común es que no todos los huecos que encontramos nos permitirán entrar. Las pruebas de penetración están limitadas en tiempo, pero siempre debemos tener en mente que, en la mayoría de los casos, **siempre hay una manera de entrar**.

Incluso después de una prueba de penetración de cuatro semanas, no podemos asegurar al 100% que no haya más vulnerabilidades. Alguien que haya estado estudiando la empresa durante meses probablemente tendrá una comprensión mucho mayor de las aplicaciones y la estructura que nosotros podríamos obtener en unas pocas semanas de evaluación. Un ejemplo reciente es el ciberataque a **SolarWinds**, lo que demuestra la necesidad de una metodología sólida que incluya la posibilidad de tales casos.

### Descripción de las Capas de la Metodología

#### **Capa 1: Presencia en Internet**

En esta capa, nos enfocamos en encontrar los objetivos que podemos investigar. Si el alcance del contrato nos permite buscar hosts adicionales, esta capa es aún más crítica. Aquí utilizamos diversas técnicas para identificar dominios, subdominios, bloques de red, y otros componentes que reflejan la presencia de la empresa en internet.

**Objetivo**: Identificar todos los sistemas e interfaces posibles que puedan ser probados.

#### **Capa 2: Gateway**

Aquí tratamos de entender cómo está protegido el objetivo alcanzable y dónde se ubica en la red. Esta capa requiere más detalles y se explorará en otros módulos debido a su diversidad y funcionalidad.

**Objetivo**: Comprender qué estamos enfrentando y de qué debemos estar atentos.

#### **Capa 3: Servicios Accesibles**

Para cada destino accesible, examinamos todos los servicios que ofrece. Cada servicio tiene un propósito específico y ha sido instalado por una razón. Para trabajar de manera efectiva con ellos, necesitamos saber cómo funcionan.

**Objetivo**: Comprender el propósito y la funcionalidad del sistema objetivo para explotarlo de manera efectiva.

#### **Capa 4: Procesos**

Cada vez que se ejecuta un comando o función, se procesan datos. Esto inicia un proceso que debe realizar tareas específicas y tiene al menos una fuente y un destino.

**Objetivo**: Entender estos factores e identificar las dependencias entre ellos.

#### **Capa 5: Privilegios**

Cada servicio se ejecuta con un usuario específico en un grupo particular, con permisos y privilegios definidos por el administrador o el sistema.

**Objetivo**: Identificar y comprender lo que es y no es posible con estos privilegios.

#### **Capa 6: Configuración del Sistema Operativo**

Aquí recolectamos información sobre el sistema operativo y su configuración utilizando acceso interno. Esto nos proporciona una visión general de la seguridad interna de los sistemas y refleja las habilidades del equipo de administración de la empresa.

**Objetivo**: Evaluar cómo los administradores gestionan los sistemas y qué información interna sensible podemos obtener.
