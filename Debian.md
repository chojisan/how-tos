# Debian 9

Table of Contents

- [How to install Apache](#how-to-install-apache)
- [How to install NGINX](#how-to-install-nginx)
- [How to install PHP](#how-to-install-php)
- [How to install Python 3.x](#how-to-install-python-3.x)
- [How to install MySQL](#how-to-install-mysql)
- [How to install PostgreSQL](#how-to-install-postgresql)
- [How to install Composer](#how-to-install-composer)
- [How to install Nodejs](#how-to-install-nodejs)
- [How to install Ruby](#how-to-install-ruby)
- [How to install Docker](#how-to-install-docker)
- [How to install Vagrant](#how-to-install-vagrant)
- [How to install Git](#how-to-install-git)

## How to install Apache

```
sudo apt update
```

```
sudo apt install apache2
```

```
sudo nano /etc/apache2/apache2.conf
```


### Adjusting the Firewall

```
sudo ufw app list
```

```
sudo ufw allow 'WWW'
```

```
sudo ufw status
```

### Checking your Web Server

```
sudo systemctl status apache2
```

```
hostname -I
```

### Managing the Apache Process

```
sudo systemctl stop apache2
sudo /etc/init.d/apache2 restart
sudo systemctl restart apache2
```

```
sudo systemctl start apache2
```

```
sudo systemctl restart apache2
```

```
sudo systemctl reload apache2
```

```
sudo systemctl disable apache2
```

```
sudo systemctl enable apache2
```

```
sudo tail -f /var/log/apache2/error.log
```

## How to install NGINX

```
sudo apt update
sudo apt install nginx
```

### Adjusting the Firewall

```
sudo ufw app list
```

```
sudo ufw allow 'Nginx HTTP'
```

```
sudo ufw status
```

### Checking your Web Server

```
systemctl status nginx
```

```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

### Managing the Nginx Process

```
sudo systemctl stop nginx
```

```
sudo systemctl start nginx
```

```
sudo systemctl restart nginx
```

```
sudo systemctl reload nginx
```

```
sudo systemctl disable nginx
```

```
sudo systemctl enable nginx
```

## How to install PHP

```
sudo apt install ca-certificates apt-transport-https
wget -q https://packages.sury.org/php/apt.gpg -O- | sudo apt-key add -
echo "deb https://packages.sury.org/php/ stretch main" | sudo tee /etc/apt/sources.list.d/php.list
```

```
/etc/php/7.0/apache2/php.ini
```

### PHP 7.1

```
sudo apt update
sudo apt install php7.1
```

### PHP 7.2

```
sudo apt update
sudo apt install php7.2
```

### PHP 7.3

```
sudo apt update
sudo apt install php7.3
```

### Install PHP Extensions
```
sudo apt install php7.3-cli php7.3-common php7.3-curl php7.3-mbstring php7.3-mysql php7.3-xml
-xdebug -intl
sudo apt-get install php7.2-bcmath

```

### Enable/Disable PHP Extension
```
sudo a2dismod extension-name
sudo a2enmod extension-name
```


## How to install Python 3.x

```
sudo apt install -y python3-pip
```

```
pip3 install package_name
```

```
sudo apt install build-essential libssl-dev libffi-dev python3-dev
```

### Setting Up a Virtual Environment

```
sudo apt install -y python3-venv
```

```
python3.5 -m venv my_env
```

```
source my_env/bin/activate
```

```
deactivate
```

## How to install MySQL

```
wget https://dev.mysql.com/get/mysql-apt-config_0.8.10-1_all.deb
```

```
sudo dpkg -i mysql-apt-config\*
```

```
sudo apt update
```

```
sudo apt install mysql-server
```

```
sudo systemctl status mysql
```

```
/usr/local/etc/my.cnf
```

### Securing MySQL

```
mysql_secure_installation
```

```
mysqladmin -u root -p version
```

```
ps aux | grep mysql
```



## How to install PostgreSQL

```
sudo -u postgres psql
```

```
postgres=# CREATE DATABASE myproject;
```

```
postgres=# CREATE USER myprojectuser WITH PASSWORD 'password';
```

```
postgres=# ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
postgres=# ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
postgres=# ALTER ROLE myprojectuser SET timezone TO 'UTC';
```

```
postgres=# GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;
```

```
postgres=# \q
```

start service
```
/etc/init.d/postgresql start

systemctl start postgresql-9.6.service 
```

```
/etc/init.d/postgresql stop

systemctl stop postgresql-9.6.service
```

```
chkconfig postgresql on
```

### Allow Remote connection over ssh

1. Open /etc/postgresql/9.6/main/pg_hba.conf and update to the following
```
# "local" is for Unix domain socket connections only
local all all trust

# IPv4 local connections:
host all all 0.0.0.0/0 trust

# IPv6 local connections:
host all all ::/0 trust

```

2. Open /etc/postgresql/9.6/main/postgresql.conf and update to the following:
```
listen_addresses = '*'
```

## How to install Composer

## How to install Nodejs

## How to install Ruby

## How to install Docker

## How to install Vagrant

## How to install Git
