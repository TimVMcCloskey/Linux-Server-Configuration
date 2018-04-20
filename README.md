# Linux-Server-Configuration
Instructions on how to set up and secure an Ubuntu Linux server on Amazon AWS and installing a web application on it.

Application URL: http://ec2-54-68-176-4.us-west-2.compute.amazonaws.com/

## About
This is the final project for the Udacity Full Stack Nano Degree. The requirements were to take a baseline installation of a Linux server and prepare it to host a web application, secure the server from a number of attack vectors, install and configure a database server, and deploy an existing web application onto it.
## Configuration steps
### Create an Ubuntu instance with Amazon Lightsail
1. Sign in to Amazon Lightsail using an Amazon Web Services account.
2. Create an instance.
3. Choose an instance image: Ubuntu - choose "OS Only".
4. Choose a payment plan - for this project pick the lowest one for free-tier access.
5. Give the instance a unique name and click "Create".
6. Wait for the instance to start up.
7. Click the networking tab and at the bottom of the page click "Add another". Add ports 123 and 2200.
### Connect to the instance on a local machine
1. Navigate to the Account Page and download the default key.
2. Save this in ~/.ssh with the name udacity.pem.
3. $ chmod 600 ~/.ssh/udacity.pem *- Make the key secure.*
4. $ ssh -i ~/.ssh/udacity.pem ubuntu@[public IP address shown on the Lightrail page] *- Log into the server*
### Upgrade packages
1. $ sudo apt-get update
2. $ sudo apt-get upgrade
3. $ sudo apt-get install finger
### Configure the firewall
1. $ sudo nano /etc/ssh/sshd_config file;   Find the Port line and change 22 to 2200.
2. $ sudo service ssh restart *-Restart ssh*
3. $ sudo ufw status *-Check for firewall*
4. $ sudo ufw default deny incoming *- Block everything coming in.*
5. $ sudo ufw default allow outgoing *- Allow everything outgoing.*
6. $ sudo ufw allow 2200/tcp
7. $ sudo ufw allow 80/tcp
8. $ sudo ufw allow 123/udp
9. $ sudo ufw enable
### Create user grader
1. $ sudo adduser grader
2. $ sudo nano /etc/sudoers.d/grader
3. Add line -grader ALL=(ALL:ALL) ALL
4. Save file to give grader sudo privileges.
### Give grader ssh login privileges
1. Open a terminal on local machine to create new keys for grader
2. $ ssh-keygen -f ~/.ssh/udacity_key.rsa *- Run on local machine.*
3. Copy the line in ~/.ssh/udacity_key.rsa.pub to the clip board.
4. $ su - grader *Switch from ubuntu to grader on server*
5. $ mkdir .ssh 
6. $ touch .ssh/authorized_keys
7. $ nano .ssh/authorized_keys
8. Paste key line from the clip board into the file and save.
9. $ sudo chmod 700 /home/grader/.ssh
10. $ sudo chmod 644 /home/grader/.ssh/authorized_keys
11. $ sudo service ssh restart
###  Enforce key-based authentication and disable root login
1. $ sudo nano /etc/ssh/sshd_config
2. Find the PasswordAuthentication line and change it to "no".
3. Find the PermitRootLogin line change it to "no".
4. $ sudo service ssh restart
5. Log out of server.
6. $ ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@54.68.176.4 *- Log back in as grader*
### Configure the local timezone to UTC
1. $ sudo dpkg-reconfigure tzdata
2. Set to UTC
### Install and configure Apache
1. $ sudo apt-get install apache2
2. $ sudo apt-get install libapache2-mod-wsgi python-dev
3. $ sudo a2enmod wsgi
4. $ sudo service apache2 restart
### Setup apps folder structure
1. cd /var/www
2. $ sudo mkdir catalog
3. $ sudo chown -R grader:grader catalog
4. $ cd catalog
5. $ mkdir catalog
### Install git, clone and setup app
1. $ sudo apt-get install git
2. $ cd catalog
3. $ git clone https://github.com/TimVMcCloskey/SongCatalog.git
4. $ mv project.py \__init__.py
### Create wsgi file
5. $ cd /var/www/catalog
6. $ nano catalog.wsgi
7. Add the following into this file  
```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'udacity'
```
### Configure virtual host
1. $ sudo nano /etc/apache2/sites-available/catalog.conf
2. Add the following into this file
```
<VirtualHost *:80>
    ServerName 54.68.176.4
    ServerAlias ec2-54-68-176-4.us-west-2.compute.amazonaws.com
    ServerAdmin timgymn@gmail.com
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
3. $ sudo a2ensite catalog.conf *- Enable the virtual host.*
4. $ sudo a2dissite 000-default.conf *- Disable the default virtual host.*
### Install all the packages needed for this application
1. $ sudo apt-get install python-pip
2. $ sudo pip install Flask
3. $ sudo pip install httplib2
4. $ sudo pip install oauth2client
5. $ sudo pip install sqlalchemy
6. $ sudo pip install psycopg2
7. $ sudo pip install sqlalchemy_utils
8. $ sudo pip install requests
9. $ sudo pip install render_template
### Set up the database
1. $ sudo su - postgres
2. $ CREATE USER catalog WITH PASSWORD songs;
3. $ ALTER USER catalog CREATEDB;
4. $ CREATE DATABASE catalog WITH OWNER catalog;
5. $ \c catalog *- Connect to the database*
6. $ REVOKE ALL ON SCHEMA public FROM public;
7. $ GRANT ALL ON SCHEMA public TO catalog;
8. $ \c *- Quit the postgrel command line*
9. $ exit
10. In \__init__.py, categories.py, and create_categories.py edit and change
```
engine = create_engine('sqlite:///SongCatalog.db')
to
engine = create_engine('postgresql://catalog:songs@localhost/catalog')
```
$ python create_categories.p *- Create initial database.*
