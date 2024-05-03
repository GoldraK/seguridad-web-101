# Código Correcto

Es fundamental ejecutar comandos a nivel de máquina únicamente si no existe otra opción en el desarrollo. Sin embargo, si se lleva a cabo, es crucial tener mucho cuidado con los parámetros que se les pasa, siempre filtrando todo y verificando correctamente que los datos proporcionados por los usuarios sean válidos.

Si observamos el código del ping en el nivel imposible, se descompone la dirección IP, se verifica cada parte por separado y luego se vuelve a armar la IP. De esta manera, nos aseguramos de que sea un dato válido.

```php
$target = stripslashes($target);
// Dividir la IP en 4 octetos
$octet = explode(".", $target);
// Verificar si cada octeto es un número entero
if ((is_numeric($octet[0])) && (is_numeric($octet[1])) && (is_numeric($octet[2])) && (is_numeric($octet[3])) && (sizeof($octet) == 4)) {
    // Si los 4 octetos son enteros, vuelva a armar la IP.
    $target = $octet[0] . '.' . $octet[1] . '.' . $octet[2] . '.' . $octet[3];
}
```
