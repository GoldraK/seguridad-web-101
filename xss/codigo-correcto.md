# Código correcto

Si queremos evitar todos los casos anteriores lo que tenemos que hacer es usar la función [htmlspecialchars](http://php.net/manual/es/function.htmlspecialchars.php), la cual convierte caracteres especiales en identidades HTML, evitando la ejecución de código, los navegadores modernos tienen filtros para evitar ataques XSS, pero siempre es mejor realizar la aplicación segura desde la base.
