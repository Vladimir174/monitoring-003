Приложил конфиги.

Всес шаги стандартные. Установлена ВМ с centos 7.

Отключеные firewalld и selinux. Для тестовой ВМ.

Установка Nginx, php-fpm и MySQL:

## Шаг 1: 

sudo yum -y update

## Шаг 2: Nginx

sudo yum -y install nginx
sudo systemctl start nginx
sudo systemctl enable nginx

## Шаг 3: MySQL (MariaDB)

sudo yum -y install mariadb-server mariadb
sudo systemctl start mariadb
sudo systemctl enable mariadb

sudo mysql_secure_installation

## Шаг 4: PHP и php-fpm

sudo yum -y install php php-mysqlnd php-fpm

## Шаг 5: Nginx для использования php-fpm

sudo nano /etc/nginx/conf.d/my_wordpress.conf


nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/html/wordpress;

    location / {
         try_files $uri $uri/ /index.php$is_args$args;
    }
    
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}


## Шаг 6: Запуск php-fpm

sudo systemctl start php-fpm
sudo systemctl enable php-fpm

## Шаг 7: Создайте базу данных для WordPress

sudo mysql -u root -p
CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
EXIT;

## Шаг 8: WordPress

wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
sudo cp -R wordpress/* /var/www/html/


sudo chown -R nginx:nginx /var/www/html/

