# CSP

CSP (Política de Seguridad de Contenido) es un estándar de seguridad creado para ayudar a prevenir XSS y otros tipos de ataques de inyección de contenido. Evitando lo siguiente:

* Los scripts externos que no están alojados en el mismo dominio o dominós permitidos por el CSP
* Elementos de script en línea, como _\<script>_
* JavaScript evaluado, como _\<img scr='x' onerror='alert(1)'>_
* Elementos básicos, que podrían romper los scripts cargados desde una ruta relativa
* Elementos de objeto, que pueden alojar contenido interactivo, como Flash

La política se implementa por medio de encabezados que son enviados con la respuesta del servidor. A partir de ahí, depende de los navegadores el tomar esa política y bloquear las violaciones a la misma que sean detectadas.

## Bajo

Acedemos al nivel y vemos un input que nos dice que podemos incluir scripts de fuentes externas.

![](<.gitbook/assets/image (4).png>)

Tenemos dos maneras de ver las políticas del CSP, mirando las cabeceras que vienen en la petición o tirando un script y mirando la consola de error.

![Cabecera petición](<.gitbook/assets/image (6).png>)

![Error en la consola](<.gitbook/assets/image (3).png>)

Como podemos ver tenemos una serie de dominios permitidos :

* Nosotros mismo
* pastebin
* example.com
* code.jquery.com
* google-analytics.com

Para resolver el problema tenemos dos caminos, encontrar otra vulnerabilidad que nos permitiera subir un fichero malicioso al servidor en cuestión, por lo que ya estaríamos ejecutando código desde nosotros mismo o lanzar código desde uno de los otros dominios.

Como disponemos del dominio pastebin entre los dominios permitidos vamos a realizar un Paste ([https://pastebin.com/9s0yDkAx](https://pastebin.com/raw/9s0yDkAx)) y lo vamos a introducir en el _input (_[_https://pastebin.com/raw/9s0yDkAx_](https://pastebin.com/raw/9s0yDkAx)_)_. Ejecutando nuestro código&#x20;

![](<.gitbook/assets/image (5).png>)

## Medio

Subimos el nivel y volvemos a ver que políticas de CSP tiene la página&#x20;

![](<.gitbook/assets/image (1).png>)

Como vemos en este caso no tenemos dominios permitidos pero si tenemos dos políticas nuevas:

* **unsafe-inline**. Permite el uso de elementos en linea como _\<script>,_ JavaScript urls.
* &#x20;**nonce-\<base64-value>.** Es una lista blanca para scripts en línea específicos que utilizan una fuente criptográfica (número usado una vez). La especificación de nonce hace que un navegador moderno ignore 'unsafe-inline' que aún podría configurarse para los navegadores antiguos sin soporte de nonce.

Con estas dos nuevas políticas si el navegador es antiguo simplemente realizando el ataque simple de XSS seria suficiente.

```javascript
<script>alert(1)</script>
```

&#x20;En un navegador moderno tendríamos que realizar el ataque añadiendo el nonce y su base64 correspondiente.

```javascript
<script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=">alert("medium")</script>
```

Obteniendo nuestra ejecución de código

![](<.gitbook/assets/image (2).png>)

