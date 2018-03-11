# Linux-Server-Configuration
## Overview

This project takes a baseline installation of a Linux distribution on a virtual machine and prepares it to host a web application. In this projec I used Lightsail - Amazon AWS to deploy the web application.


## HTTP
52.29.193.94

## SSH

To login to the server execute this command:

ssh -p 2200 -i [RSA_FILE] grader@52.29.193.94


## Configration

### Add user
Add user grader : sudo useradd grader

### Give the user grader permission to sudo

echo "grader ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/grader

### Update and upgrade all installed packages
apt-get update

apt-get upgrade
### Set-up SSH keys for user grader
mkdir /home/grader/.ssh
chown grader:grader /home/grader/.ssh
chmod 700 /home/grader/.ssh
cp /root/.ssh/authorized_keys /home/grader/.ssh/
chown grader:grader /home/grader/.ssh/authorized_keys
chmod 644 /home/grader/.ssh/authorized_keys

To login into server enter this command:
ssh -i ~/.ssh/udacity_key.rsa grader@52.29.193.94

### Change SSH port from 22 to 2200

Edit the file /etc/ssh/sshd_config and change the line Port 22 to: Port 2200

### Time Zone Configuration

sudo dpkg-reconfigure tzdata
### Fire wall settings

Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

Block all incoming connections:

sudo ufw default deny incoming

Allow outgoing connection on all ports:

sudo ufw default allow outgoing

Allow incoming connection for SSH on port 2200:

sudo ufw allow 2200/tcp

Allow incoming connections for HTTP on port 80:

sudo ufw allow www

Allow incoming connection for NTP on port 123:

sudo ufw allow ntp sudo ufw enable

### Postgresql installation

sudo apt-get install postgresql postgresql-contrib

### Database configration:
Create a PostgreSQL user called catalog with:

sudo -u postgres createuser -P salma

You are prompted for a password. This creates a normal user that can't create databases, roles (users).

Create an empty database called salma with:

sudo -u postgres createdb -O salma salma

Using command

sudo -u postgres psql
create user salma with password 'salma';
create database salma with owner salma;

### Install required modules
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install requests
sudo pip install httplib2

### Install Git version control software

sudo apt-get install git

### Clone the repository

In /var/www/ clone repo using this command:
	https://github.com/salmaBakr/Item-Catalog-Application.git
for simplicity rename directory:
sudo mv Item-Catalog-Application Catalog

### Create .wsgi file

catalog.wsgi was created in /var/www/Catalog/
import sys
sys.path.insert(0,"/var/www/Catalog/")
from project import app as application
application.secret_key = 'SECRET_KEY'

Add /etc/apache2/sites-enabled/Catalog.conf to tell apache to run the catalog.wsgi file as default

## APache2 Configration to serve app
Add the following lines of code to the file to configure the virtual host. Be sure to change the ServerName to your domain or cloud server's IP address:

<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port t$
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com
        ErrorLog /home/grader/apache_errors.log
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/Catalog
        # Define WSGI parameters. The daemon process runs as the www-data user.
        WSGIDaemonProcess catalog user=www-data group=www-data threads=5
        WSGIProcessGroup catalog
        WSGIApplicationGroup %{GLOBAL}

        # Define the location of the app's WSGI file
        WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
        # Allow Apache to serve the WSGI app from the catalog app directory
        <Directory /var/www/Catalog/>
                Require all granted
        </Directory>

        # Setup the static directory (contains CSS, Javascript, etc.)
        Alias /static /var/www/Catalog/

        # Allow Apache to serve the files from the static directory
        <Directory  /var/www/Catalog/>
                Require all granted
        </Directory>

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

### References
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps#step-five-%E2%80%93-create-the-wsgi-file

https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04