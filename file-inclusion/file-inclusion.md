# LFI

### Bajo

Como dicho ataque consiste en poder cargar archivos locales, podemos ir a la url principal y buscar el robots.txt para las pruebas, si existe lo que tenemos que hacer es averiguar donde esta, esto lo hacemos entrando y saliendo de los directorios, por lo que si ponemos el siguiente codigo

```
../../robots.txt
```

Podemos observar que carga el fichero con los permisos para los robots.

![](<../.gitbook/assets/4 (10).png>)

Ahora lo que tenemos que hacer es seguir buscando los diferentes niveles de carpetas hasta llegar a información interesante como el _/etc/passwd_

```
../../../../../etc/paswd
```

![](<../.gitbook/assets/5 (5).png>)

### Medio

Cambiamos el nivel y si volvemos a probar la ruta de robots, vemos que esta vez no funciona

![](<../.gitbook/assets/1 (11).png>)

Si nos fijamos en el código , vemos que hace una sustitución con str\_replace no dejándonos usar el ../ para movernos por los directorios

```php
$file = str_replace( array( "../", "..\"" ), "", $file );
```

Pero como str\_replace no es recursivo, podemos jugar con ello cambiando los el movimiento de carpetas por esto

```
....//
```

Lo que sucederá es que se eliminara el _../_ dejando el que lo envuelve en su lugar, volviendo a funcionar el ataque.

![](<../.gitbook/assets/2 (21).png>)

### Alto <a href="#lfihigh" id="lfihigh"></a>

Vamos con el ultimo nivel, volvemos a probar los ataques de antes

![](<../.gitbook/assets/1 (8).png>)

![](<../.gitbook/assets/2 (17).png>)

Y como vemos no nos funcionan ninguno de los dos, al fijarnos en el código

```php
if( !fnmatch( "file*", $file ) && $file != "include.php" ) {
    // This isn't the page we want!
    echo "ERROR: File not found!";
    exit;
}
```

vemos que usa la función fnmatch para comprobar si en la ruta existe la palabra file, por lo que podemos usar file:// que da acceso al sistema de ficheros local en php y contiene la palabra file

```
file:///var/www/html/robots.txt
```

![](<../.gitbook/assets/3 (10).png>)

```
file:///etc/passwd
```

![](<../.gitbook/assets/4 (11).png>)

## &#x20;<a href="#rfi" id="rfi"></a>
