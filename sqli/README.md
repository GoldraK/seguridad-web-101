# SQLi

La inyección SQL (SQL Injection) es un método de infiltración de código malicioso que aprovecha una vulnerabilidad informática en una aplicación a nivel de validación de entradas para realizar operaciones sobre una base de datos. Esta vulnerabilidad surge debido a un control o filtrado inadecuado de las variables utilizadas en un programa que contiene o genera código SQL. De hecho, es un error de una categoría más amplia de vulnerabilidades que puede ocurrir en cualquier lenguaje de programación o script embebido en otro. La inyección SQL se conoce indistintamente como el tipo de vulnerabilidad, el método de infiltración, el acto de incrustar código SQL malicioso y la parte del código incrustado.

Por ejemplo, supongamos que el siguiente código se encuentra en una aplicación web y que existe un parámetro "nombreUsuario" que contiene el nombre de usuario a consultar. Una inyección SQL podría provocarse de la siguiente manera:

El código SQL original y vulnerable es:

```sql
consulta := "SELECT * FROM usuarios WHERE nombre = '" + nombreUsuario + "';"
```

Si el operador escribe un nombre, por ejemplo "Alicia", no ocurrirá nada anormal. La aplicación generaría una sentencia SQL similar a la siguiente, que es perfectamente correcta, donde se seleccionarían todos los registros con el nombre "Alicia" en la base de datos:

```sql
SELECT * FROM usuarios WHERE nombre = 'Alicia';
```

Sin embargo, si un operador malintencionado escribe como nombre de usuario a consultar:

```sql
Alicia'; DROP TABLE usuarios; SELECT * FROM datos WHERE nombre LIKE '%
```

Se generaría la siguiente consulta SQL:

```sql
SELECT * FROM usuarios WHERE nombre = 'Alicia';
DROP TABLE usuarios;
SELECT * FROM datos WHERE nombre LIKE '%';
```

En la base de datos, las consultas se ejecutarían en el orden dado: se seleccionarían todos los registros con el nombre 'Alicia', se eliminaría la tabla 'usuarios' y, finalmente, se seleccionaría toda la tabla "datos", que no debería estar disponible para los usuarios web comunes.

En resumen, cualquier dato de la base de datos podría quedar expuesto para ser leído o modificado por un usuario malintencionado. Para prevenir esto, es fundamental seguir prácticas de [programación segura](codigo-correcto.md), como el uso de consultas preparadas y la validación adecuada de las entradas del usuario.
