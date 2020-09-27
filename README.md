# Debian Server Set Up for LAMP Server (Apache, PHP, MariaDB)

In this guide we will set up clean Debian server for PHP projects. install from Debian repositories and from sources all needed packages and ware it together for working PHP projects.

[Youtube video guide (in Russian)](https://www.youtube.com/watch?v=LvvSlljb8Yw)

## Setup SSH

change user on su

add repo from yandex

https://chip-and-del.ru/%D0%A0%D0%B5%D0%BF%D0%BE%D0%B7%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%B8-debian-10-x/

```
nano /etc/apt/sources.list

and add

# buster
 
deb http://security.debian.org/ buster/updates main
# Line commented out by installer because it failed to verify:
deb-src http://security.debian.org/ buster/updates main
 
deb http://mirror.yandex.ru/debian buster main
deb-src http://mirror.yandex.ru/debian buster main
 
deb http://mirror.yandex.ru/debian buster-updates main
deb-src http://mirror.yandex.ru/debian buster-updates main
 
deb http://mirror.yandex.ru/debian/ buster-proposed-updates main non-free contrib
deb-src http://mirror.yandex.ru/debian/ buster-proposed-updates main non-free contrib
 
# Backports
#deb http://mirror.yandex.ru/debian buster-backports main contrib non-free
#deb-src http://mirror.yandex.ru/debian buster-backports main contrib non-free
```

```
apt-get update -y  && apt-get dist-upgrade -y && apt-get autoremove -y && apt-get autoclean -y
```

```
apt-get install -y htop git curl wget unzip zip gcc
```

## MariaDB

```
sudo apt install -y mariadb-server mariadb-client
sudo mysql_secure_installation
```

Login with `root` user is available just with `sudo`:

```
sudo mariadb -u root
```

So, create database and user and give him privileges to new database:

```
sudo mariadb -u root
CREATE DATABASE new_db_name COLLATE 'utf8_general_ci';
CREATE USER new_db_user IDENTIFIED BY 'some_password';
GRANT ALL privileges ON new_db_name .* TO new_db_user;
```

## Apache, PHP

Install Apache, create `code` directory in user home directory:

```
sudo apt install -y apache2 apache2-utils
```

Now install PHP:

on debian 10 add repo



```
sudo apt install -y php7.0 libapache2-mod-php7.0 php7.0-mysql php-common php7.0-cli php7.0-common php7.0-json php7.0-opcache php7.0-readline php7.0-mbstring php7.0-xml php7.0-gd
```

Enable `mod_php` in Apache:

```
sudo a2enmod php7.0
sudo systemctl restart apache2
```

Configure Apache:

```
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/newproject.conf

sudo vim /etc/apache2/sites-available/newproject.conf
```

Change or append lines:

```
        DocumentRoot /home/www/code/newproject
        ServerName newproject.your-site.ru
```

Enable site config in Apache, enable `mod_rewrite`:

```
sudo a2ensite newproject.conf

sudo a2enmod rewrite
```

Configure Apache for `.htaccess` files:

```
sudo vim /etc/apache2/apache2.conf
```

Change lines:

```
<Directory />
    Options FollowSymLinks
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
```

Restart Apache:

```
sudo service apache2 restart
```


