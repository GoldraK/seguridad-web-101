# Código correcto

La manera correcta de hacer las consultas a la base de datos y evitar problemas de inyección de SQL es utilizando PDO en PHP. Otros lenguajes de programación tendrán sus propias herramientas para prevenir inyecciones de SQL, por lo que es recomendable revisarlas antes de trabajar con bases de datos.&#x20;

En PHP, [PDO](https://www.php.net/manual/es/class.pdo.php) (PHP Data Objects) es una interfaz para acceder a bases de datos que proporciona un conjunto de clases y métodos que permiten realizar consultas y manipulaciones de datos de forma segura. A continuación, se muestran algunos ejemplos de cómo realizar consultas a la base de datos utilizando PDO en PHP de manera segura:

1. Conexión a la base de datos utilizando PDO:

```php
<?php
$servername = "localhost";
$username = "your_username";
$password = "your_password";
$dbname = "your_database";

try {
    $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    echo "Error: " . $e->getMessage();
}
?>
```

2. Consulta segura utilizando prepared statements:

```php
<?php
// Preparar la consulta
$stmt = $conn->prepare("SELECT * FROM users WHERE email = :email AND password = :password");

// Asignar valores a los parámetros
$stmt->bindParam(':email', $email);
$stmt->bindParam(':password', $password);

// Ejecutar la consulta
$stmt->execute();

// Obtener los resultados
$result = $stmt->fetch(PDO::FETCH_ASSOC);
?>
```

3. Insertar datos de forma segura:

```php
<?php
// Preparar la consulta
$stmt = $conn->prepare("INSERT INTO users (username, email, password) VALUES (:username, :email, :password)");

// Asignar valores a los parámetros
$stmt->bindParam(':username', $username);
$stmt->bindParam(':email', $email);
$stmt->bindParam(':password', $password);

// Ejecutar la consulta
$stmt->execute();
?>
```

4. Actualizar datos de forma segura:

```php
<?php
// Preparar la consulta
$stmt = $conn->prepare("UPDATE users SET email = :email, password = :password WHERE id = :id");

// Asignar valores a los parámetros
$stmt->bindParam(':email', $email);
$stmt->bindParam(':password', $password);
$stmt->bindParam(':id', $id);

// Ejecutar la consulta
$stmt->execute();
?>
```

5. Eliminar datos de forma segura:

```php
<?php
// Preparar la consulta
$stmt = $conn->prepare("DELETE FROM users WHERE id = :id");

// Asignar valores a los parámetros
$stmt->bindParam(':id', $id);

// Ejecutar la consulta
$stmt->execute();
?>
```

Utilizando PDO y prepared statements, puedes proteger tus consultas a la base de datos de inyecciones de SQL. Sin embargo, es importante seguir investigando y mantenerse actualizado sobre las mejores prácticas y las vulnerabilidades más recientes para garantizar la seguridad de tus aplicaciones. Visita las páginas de OWASP y otros recursos confiables para aprender más sobre las inyecciones

