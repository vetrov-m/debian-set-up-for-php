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

## Apache

Install Apache, create `code` directory in user home directory:

```
sudo apt install -y apache2 apache2-utils
```

## Now install PHP:

Add SURY PHP PPA repository

Download and store PPA repository in a file on your Debian Server/Desktop. But first, download GPG key.

```
sudo apt -y install lsb-release apt-transport-https ca-certificates 
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
```

Then add repository.

```
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list
```
```
sudo apt update
```
```
sudo apt install -y php7.0 libapache2-mod-php7.0 php7.0-mysql php-common php7.0-cli php7.0-common php7.0-json php7.0-opcache php7.0-readline php7.0-mbstring php7.0-xml php7.0-gd
```

Enable `mod_php` in Apache:

```
sudo a2enmod php7.0
sudo systemctl restart apache2
```

if a2enmod php7.0 message on 'command not found' 

It's a PATH variable bug.

Try this:

```
apt-get install mlocate
updatedb
locate a2enmod
```

the output is:
```
/usr/sbin/a2enmod
```

then
```
grep PATH /etc/profile

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/sbin"
PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
Export PATH
```

As you can see, /usr/sbin in not in root $PATH

So, edit the first line to
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/sbin"
```

if you need disable phpmod

```
sudo a2dismod
```

## Configure Apache:

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


