# Debian 9

## How to install Apache

sudo apt update

sudo apt install apache2

### Adjusting the Firewall

sudo ufw app list

sudo ufw allow 'WWW'

sudo ufw status


### Checking your Web Server

sudo systemctl status apache2

hostname -I


### Managing the Apache Process

sudo systemctl stop apache2

sudo systemctl start apache2

sudo systemctl restart apache2

sudo systemctl reload apache2

sudo systemctl disable apache2

sudo systemctl enable apache2



## How to install NGINX

sudo apt update
sudo apt install nginx

### Adjusting the Firewall

sudo ufw app list

sudo ufw allow 'Nginx HTTP'

sudo ufw status

### Checking your Web Server

systemctl status nginx

ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'


### Managing the Nginx Process

sudo systemctl stop nginx

sudo systemctl start nginx

sudo systemctl restart nginx

sudo systemctl reload nginx

sudo systemctl disable nginx

sudo systemctl enable nginx


## How to install PHP 7.1

## How to install PHP 7.2

## How to install Python 3.x

sudo apt install -y python3-pip
pip3 install package_name

sudo apt install build-essential libssl-dev libffi-dev python3-dev

### Setting Up a Virtual Environment

sudo apt install -y python3-venv

python3.5 -m venv my_env

source my_env/bin/activate

deactivate



## How to install MySQL

wget https://dev.mysql.com/get/mysql-apt-config_0.8.10-1_all.deb

sudo dpkg -i mysql-apt-config*

sudo apt update

sudo apt install mysql-server

sudo systemctl status mysql

### Securing MySQL

mysql_secure_installation

mysqladmin -u root -p version


## How to install PostgreSQL

sudo -u postgres psql

postgres=# CREATE DATABASE myproject;

postgres=# CREATE USER myprojectuser WITH PASSWORD 'password';

postgres=# ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
postgres=# ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
postgres=# ALTER ROLE myprojectuser SET timezone TO 'UTC';

postgres=# GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;

postgres=# \q

