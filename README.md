# How to Setup Codeigniter on Ubuntu 22.04

- sudo apt update
- sudo apt install curl unzip
- sudo apt install nginx
- sudo apt install mariadb-server mariadb-client
- sudo apt install php php-cli php-mysql php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath php-intl php-fpm

## Setup SQL (Mariadb)

- sudo mysql_secure_installation
- sudo mysql
- CREATE USER  "user"@"localhost" IDENTIFIED BY "password";
- GRANT ALL ON *.* TO "user"@"localhost";
- FLUSH PRIVILEGES;
- EXIT;

- mysql -uuser -p
- CREATE DATABASE ci4;
- EXIT;

## Setup PHP Composer

- cd ~
- curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
- HASH=`curl -sS https://composer.github.io/installer.sig`
- echo $HASH
- php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
- sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
- composer

## Setup Codeigniter

- cd /var/www
- sudo chown $USER:$USER .
- composer create-project codeigniter4/appstarter ci4
- sudo chown -Rv www-data /var/www/ci4/writable

## Setup Nginx
- sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default1
- sudo vim /etc/nginx/sites-available/default
```
server {
    listen 80;
    listen [::]:80;

    server_name example.com;

    root  /var/www/example.com/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;

        # With php-fpm:
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        # With php-cgi:
        # fastcgi_pass 127.0.0.1:9000;
    }

    error_page 404 /index.php;

    # deny access to hidden files such as .htaccess
    location ~ /\. {
        deny all;
    }
}
```
- Save

- sudo systemctl restart nginx
