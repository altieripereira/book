# Roteiro instação MOODLE 4.3 + NGINX


## Instalação do PHP 8 FPM

```bash
sudo add-apt-repository ppa:ondrej/php
```

```bash
sudo apt update
sudo apt upgrade
```

```bash
sudo apt install php8.0-fpm php8.0-cli php8.0-gd php8.0-curl php8.0-mysql php8.0-xmlrpc php8.0-intl php8.0-zip php8.0-mbstring php8.0-soap unzip curl
```

```bash
sudo nano /etc/php/8.0/fpm/php.ini
```

```plaintext
memory_limit = 256M
upload_max_filesize = 100M
post_max_size = 100M
max_execution_time = 180
max_input_vars = 5000
```

```bash
sudo systemctl restart php8.0-fpm
```

## Instalação do NGINX

```bash
sudo apt install nginx
sudo nano /etc/nginx/sites-available/moodle
```

```plaintext
server {
    listen 80;
    server_name seu_dominio.com;
    root /var/www/html/moodle;
    index index.php index.html index.htm;
    fastcgi_index index.php;
    access_log /var/log/nginx/moodle_access.log;
    error_log /var/log/nginx/moodle_error.log;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location ~ ^(.+\.php)(.*)$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    location ~ /\.ht {
        deny all;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/moodle /etc/nginx/sites-enabled/
```

## Donwload do moodle versão 4.3++

```bash
sudo mkdir /var/www/html/moodle
sudo curl -L https://download.moodle.org/download.php/direct/stable43/moodle-latest-43.zip -o /tmp/moodle-latest-43.zip
sudo unzip /tmp/moodle-latest-43.zip -d /var/www/html/
sudo chown -R www-data:www-data /var/www/html/moodle
sudo chmod -R 755 /var/www/html/moodle
```

```bash
sudo systemctl restart nginx
```

## Instalação do MariaDB Server - 10.06

```bash
sudo apt-get install apt-transport-https curl
sudo mkdir -p /etc/apt/keyrings
sudo curl -o /etc/apt/keyrings/mariadb-keyring.pgp 'https://mariadb.org/mariadb_release_signing_key.pgp'
```

```bash
/etc/apt/sources.list.d/mariadb.sources
```
```plaintext
# MariaDB 10.6 repository list - created 2024-03-15 14:46 UTC
# https://mariadb.org/download/
X-Repolib-Name: MariaDB
Types: deb
# deb.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# URIs: https://deb.mariadb.org/10.6/ubuntu
URIs: https://atl.mirrors.knownhost.com/mariadb/repo/10.6/ubuntu
Suites: jammy
Components: main main/debug
Signed-By: /etc/apt/keyrings/mariadb-keyring.pgp
```

```bash
sudo apt-get update
sudo apt-get install mariadb-server
```

```bash
sudo mysql -u root -p
```

## Criação do banco de dados para o moodle
```sql
CREATE DATABASE moodle CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'moodleuser'@'localhost' IDENTIFIED BY 'sua_senha';
GRANT ALL PRIVILEGES ON moodle.* TO 'moodleuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
