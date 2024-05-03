# RFI

### Bajo <a href="#rfilow" id="rfilow"></a>

Volvemos a empezar y este caso vamos a inyectar un fichero remoto, como ejemplo vamos a usar google, como sabemos que carga lo que le pases por parámetros vamos a introducir la url completa y ver que sucede.

![](<../.gitbook/assets/1 (22).png>)

Como vemos nos carga google en nuestra web.

### Medio <a href="#rfimedium" id="rfimedium"></a>

Cambiamos de nivel y volvemos a probar el dominio de google, dejando de funcionar

![](<../.gitbook/assets/1 (21).png>)

Si nos fijamos en el código nos damos cuenta de que tiene un srt\_replace como en LFI que nos impide usar http o https

```php
// Input validation 
$file = str_replace( array( "http://", "https://" ), "", $file );
```

Pero como str\_replace no es recursivo, podemos jugar con ello cambiando la manera de escribir el protocolo

```
htthttp://p://google.es
```

Lo que sucederá es que se eliminara el _http://_ dejando el que lo envuelve en su lugar, volviendo a funcionar el ataque.

![](<../.gitbook/assets/2 (2).png>)

### Alto <a href="#rfihigh" id="rfihigh"></a>

En este caso no se puede resolver incluyendo una url, por lo que hay que usar otra vulnerabilidad como file upload y llamar al código desde ese fichero.
