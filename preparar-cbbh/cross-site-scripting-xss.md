# Cross-Site-Scripting (XSS)

{% file src="../.gitbook/assets/Cross_Site_Scripting_Xss_Module_Cheat_Sheet.pdf" %}

### Tipos de XSS

Hay tres tipos principales de vulnerabilidades XSS:

| Tipo                             | Descripción                                                                                                                                                                                                                                                                                |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Stored (Persistent) XSS`        | El tipo más crítico de XSS, que ocurre cuando la entrada del usuario se almacena en la base de datos de back-end y luego se muestra en recuperación (por ejemplo, publicaciones o comentarios)                                                                                             |
| `Reflected (Non-Persistent) XSS` | Ocurre cuando la entrada del usuario se muestra en la página después de ser procesada por el servidor de backend, pero sin ser almacenado (por ejemplo, resultado de búsqueda o mensaje de error)                                                                                          |
| `DOM-based XSS`                  | Otro tipo XSS no persistente que ocurre cuando la entrada del usuario se muestra directamente en el navegador y se procesa completamente en el lado del cliente, sin llegar al servidor de back-end (por ejemplo, a través de parámetros HTTP del lado del cliente o etiquetas de anclaje) |
