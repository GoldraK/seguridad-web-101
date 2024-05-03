# Command injection

La inyección de comandos (command injection) es una técnica empleada a través de interfaces web que permite la ejecución de comandos del sistema operativo en un servidor web. Esto puede brindar a un atacante la capacidad de ejecutar comandos arbitrarios en el servidor, pudiendo comprometer su seguridad.

Veamos cómo evoluciona esta técnica de inyección en diferentes niveles de seguridad:

## Bajo

En este nivel, no hay ninguna validación o filtrado de entrada del usuario. Si entramos en la sección de command injection, nos encontramos con un input para realizar pings, al que le introducimos una IP y realiza dicho ping.

![](<../.gitbook/assets/1 (15).png>)

Pero si el programador no tuvo en cuenta que el usuario puede introducir lo que quiera en este caso, se pueden concatenar comandos como en cualquier terminal.

| Comandos | Acción                                                                | ejemplo                   |
| -------- | --------------------------------------------------------------------- | ------------------------- |
| \|       | La salida del primero, es la entrada del segundo                      | cat xxx \| grep yyy       |
| \|\|     | El segundo comando se ejecutará si el primera termina sin éxito       | ping 18.2.2 \|\| cat xxxx |
| &        | Ejecutar dos o mas comandos de manera simultanea                      | ls xxx & cat yyyy         |
| &&       | El segundo comando se ejecutará solo si el primero acaba con éxito    | ping 8.8.8.8 && cat ggggg |
| ;        | El segundo comando se ejecutara sin importar el resultado del primero | ls tttt; cat yyyy         |

Gracias a esto, podemos empezar a jugar con nuestro input y, si probamos cualquiera de las anteriores combinaciones, funcionará el ataque, debido a que el código ejecuta directamente lo que introduce el usuario.

![](<../.gitbook/assets/2 (10).png>)

Como resultado, se listan los ficheros, dejando a nuestra imaginación qué hacer en la máquina objetivo.

## Medio

Cambiamos de nivel y probamos el mismo comando con el que hemos tenido éxito en el nivel anterior, pero en este caso no nos funciona.

![](<../.gitbook/assets/1 (7).png>)

En este nivel, se filtran las opciones `&&` y `;` en el código de la aplicación:

```php
    $substitutions = array(
        '&&' => '',
        ';'  => '',
    );
```

Dado que estas opciones están bloqueadas, debemos explorar otras alternativas en las tablas de comandos disponibles. Al probar la ejecución de comandos en paralelo, logramos nuevamente la inyección.

![](<../.gitbook/assets/2 (8).png>)

## Alto

Terminamos con el nivel alto y probamos todos los casos de la tabla de arriba sin resultado valido.

![](<../.gitbook/assets/1 (9).png>)

Si nos fijamos en el código, todas las opciones de los niveles anteriores están en la lista negra (blacklist):

```php
    $substitutions = array(
        '&'  => '',
        ';'  => '',
        '| ' => '',
        '-'  => '',
        '$'  => '',
        '('  => '',
        ')'  => '',
        '`'  => '',
        '||' => '',
    );
```

Sin embargo, al observar más de cerca, notamos que el operador `|` tiene un espacio después de él. Esto significa que podemos intentar ejecutar el comando sin espacio después del operador, lo que permite evadir la lista negra y lograr la inyección.

![](<../.gitbook/assets/3 (13).png>)

En resumen, la inyección de comandos del sistema operativo es una técnica peligrosa que permite a los atacantes ejecutar comandos arbitrarios en un servidor web. Para prevenir este tipo de ataques, es importante seguir las prácticas de programación segura, como la validación adecuada de las entradas del usuario y el uso de listas blancas en lugar de listas negras para filtrar comandos y caracteres no deseados.
