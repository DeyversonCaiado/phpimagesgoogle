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
