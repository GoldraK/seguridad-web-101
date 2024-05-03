# Código correcto

Como hemos visto hay que tener mucho cuidado con los ficheros que dejamos subir a nuestras aplicaciones, siempre hay que verificar que son archivos validos, para el caso de las imagenes tendriamos que usar [getimagesize](http://php.net/manual/es/function.getimagesize.php) para obtener el tamaño de la imagen y recrear la imagen para eliminar cualquier cosa malicosa que se encuentre en el mediante [imagecreatefromjpeg](http://php.net/manual/es/function.imagecreatefromjpeg.php), [imagecreatefrompng](http://php.net/manual/es/function.imagecreatefrompng.php)
