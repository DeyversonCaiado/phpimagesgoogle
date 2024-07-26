# Google Images Downloader with PHP
### This repository contains a simple script to fetch images from Google Custom Search API, display them, and provide a way to download and save them locally. The saved image path is also stored in a MySQL database.

## Getting Started
### Prerequisites
-PHP
-MySQL
-Google Custom Search API Key
-Search Engine ID

### Replace 'SUA_CHAVE_DE_API' and 'SEU_ID_DO_MECANISMO_DE_BUSCA' with your actual Google Custom Search API key and Search Engine ID.

### Directory Structure
Make sure the imagens directory exists and is writable. If it doesn't exist, the script will create it.

### Database
Create a table in your MySQL database to store the image paths:

```sql
CREATE TABLE imagens (
    id INT AUTO_INCREMENT PRIMARY KEY,
    url_local VARCHAR(255) NOT NULL
);
```
### PHP Script
```php
<?php

// Function to get images from Google
function getGoogleImages($productName, $apiKey, $searchEngineId) {
    $query = urlencode($productName);
    $url = "https://www.googleapis.com/customsearch/v1?q={$query}&cx={$searchEngineId}&key={$apiKey}&searchType=image&num=10";

    $response = file_get_contents($url);
    if ($response === FALSE) {
        die('Error occurred while fetching data from Google API');
    }

    $data = json_decode($response, true);
    if (isset($data['items'])) {
        return array_map(function($item) {
            return $item['link'];
        }, $data['items']);
    } else {
        return [];
    }
}

// Function to download image and save locally
function downloadImage($imageUrl, $saveDir) {
    $urlParts = parse_url($imageUrl);
    $imageName = basename($urlParts['path']);
    $savePath = $saveDir . '/' . $imageName;

    file_put_contents($savePath, file_get_contents($imageUrl));
    return $savePath;
}

// API configuration
$apiKey = 'SUA_CHAVE_DE_API';
$searchEngineId = 'SEU_ID_DO_MECANISMO_DE_BUSCA';

// Product name to search
$productName = 'Nome do Produto';

// Directory to save images
$saveDir = 'imagens';

// Create directory if it doesn't exist
if (!file_exists($saveDir)) {
    mkdir($saveDir, 0777, true);
}

// Get images from Google
$images = getGoogleImages($productName, $apiKey, $searchEngineId);

if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['image_url'])) {
    $imageUrl = $_POST['image_url'];
    $localPath = downloadImage($imageUrl, $saveDir);
    
    // Database connection (replace with your connection details)
    $pdo = new PDO('mysql:host=localhost;dbname=nome_do_banco', 'usuario', 'senha');
    $sql = 'INSERT INTO imagens (url_local) VALUES (:url_local)';
    $stmt = $pdo->prepare($sql);
    $stmt->bindParam(':url_local', $localPath);
    $stmt->execute();

    echo "Imagem salva com sucesso: " . $localPath;
}

?>

<!DOCTYPE html>
<html>
<head>
    <title>Busca de Imagens</title>
</head>
<body>
    <h1>Imagens de <?php echo htmlspecialchars($productName); ?></h1>
    <ul>
        <?php foreach ($images as $image): ?>
            <li>
                <img src="<?php echo $image; ?>" alt="Imagem" style="width: 150px; height: auto;">
                <form method="post" action="">
                    <input type="hidden" name="image_url" value="<?php echo $image; ?>">
                    <button type="submit">Baixar Imagem</button>
                </form>
            </li>
        <?php endforeach; ?>
    </ul>
</body>
</html>

```

### How It Works
- Function getGoogleImages:
    - Fetches images from Google using the Custom Search JSON API.
- Function downloadImage:
    - Downloads the image and saves it locally, handling special characters in URLs.
- Form Handling:
    - Handles the form submission to download the image and save the path to the database.
### Notes
- Security:
    - Ensure to validate and sanitize all inputs to prevent SQL injection and other security issues.
- Error Handling:
    - Add robust error handling to manage API request failures and file operations.
