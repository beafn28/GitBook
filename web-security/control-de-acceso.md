# Control de Acceso

### **¿ Qué es el Control de Acceso?**

El **control de acceso** es la aplicación de restricciones sobre quién o qué está autorizado para realizar acciones o acceder a recursos. En el contexto de aplicaciones web, depende de tres factores clave:

1. **Autenticación** – Verifica que el usuario es quien dice ser.
2. **Gestión de sesiones** – Identifica qué solicitudes HTTP pertenecen a ese mismo usuario.
3. **Control de acceso** – Determina si el usuario tiene permiso para realizar la acción solicitada.

Las fallas en el control de acceso son comunes y representan una vulnerabilidad de seguridad crítica. Diseñar y gestionar estos controles es un desafío complejo, ya que implica restricciones empresariales, organizativas y legales en una implementación técnica. Dado que estas decisiones deben ser tomadas por personas, existe un alto riesgo de errores.

### **Funcionalidad No Protegida**

El **escalamiento vertical de privilegios** ocurre cuando una aplicación no implementa protecciones adecuadas para funciones sensibles.

Por ejemplo, una página de administración puede estar vinculada solo desde el panel de un administrador, pero un usuario regular podría acceder a estas funciones si conoce la URL correspondiente.

Un sitio web puede alojar funciones administrativas en:\
`https://insecure-website.com/admin`

Si la aplicación no impone restricciones, cualquier usuario podría acceder simplemente ingresando la URL en su navegador.

#### **Otras formas de descubrir URLs sensibles:**

Revisando el archivo `robots.txt`:\
`https://insecure-website.com/robots.txt`

Usando ataques de fuerza bruta con listas de palabras para encontrar rutas ocultas.

```

var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-a6hrd8');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}

```
