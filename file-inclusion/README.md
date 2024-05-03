# File Inclusion

Una vulnerabilidad de inclusión de archivos (File Inclusion) es un tipo de vulnerabilidad que, con mayor frecuencia, afecta las aplicaciones web que dependen de un tiempo de ejecución de scripts.

Este problema se produce cuando una aplicación crea una ruta de acceso al código ejecutable utilizando una variable controlada por el atacante de forma que permite al atacante controlar qué archivo se ejecuta en tiempo de ejecución.

Tenemos dos ataques posibles LFI o RFI

* Local File Inclusion (LFI) - Nos permite cargar ficheros locales.
* Remote File Inclusion (RFI)  - Nos permite cargar ficheros de manera remota.

Si entramos en la sección nos encontramos con una lista de ficheros.

![](<../.gitbook/assets/1 (2).png>)

Los cuales si hacemos click sobre ellos se nos cargan, ejecutando su código.

![](<../.gitbook/assets/2 (12).png>)

Si nos fijamos en la url, vemos que es el nombre del fichero que acabamos de hacer click

![](<../.gitbook/assets/3 (1).png>)
