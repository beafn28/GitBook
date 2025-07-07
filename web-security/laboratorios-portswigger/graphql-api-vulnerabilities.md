# GraphQL API vulnerabilities

## Lab: Accessing private GraphQL posts

### Enunciado

La página del blog de este laboratorio contiene una entrada oculta que tiene una contraseña secreta. Encuentra la entrada de blog oculta y escribe la contraseña.

### Resolución

<figure><img src="../../.gitbook/assets/image (1535).png" alt=""><figcaption></figcaption></figure>

Vemos que en la respuesta falta el post 3. Mandamos la petición al **Repeater**. Nos damos cuenta que si cambiamos el **ID** del post en la petición a **3** nos sale el post en cuestión.

<figure><img src="../../.gitbook/assets/image (1536).png" alt=""><figcaption></figcaption></figure>

Ahora para conseguir la contraseña vamos a la sección de GraphSQL.

<figure><img src="../../.gitbook/assets/image (1537).png" alt=""><figcaption></figcaption></figure>

Buscamos referencia a password y volvemos a la petición de antes. Modificamos consulta pegando la referencia de password.

<figure><img src="../../.gitbook/assets/image (1538).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1539).png" alt=""><figcaption></figcaption></figure>
