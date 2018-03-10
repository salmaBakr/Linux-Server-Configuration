# Linux-Server-Configuration
Overview

This project takes a baseline installation of a Linux distribution on a virtual machine and prepares it to host a web application.

1. Make the required installation and updates.
2. Clone and setup the Item Catalog

Clone repository :https://github.com/salmaBakr/Item-Catalog-Application.git

3. Add /etc/apache2/sites-enabled/Catalog.conf to tell apache to run the catalog.wsgi file as default

4. Make the wsgi file in the directory /var/www/Catalog and adding this code to it to run project.py

## HTTP
52.29.193.94

## SSH

To login to the server execute this command:

ssh -p 2200 -i [RSA_FILE] grader@52.29.193.94
