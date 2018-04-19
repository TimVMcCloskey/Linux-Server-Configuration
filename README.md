# Linux-Server-Configuration
Instructions on how to set up and secure an Ubuntu Linux server on Amazon AWS and installing a web application on it.

Application URL: http://ec2-54-68-176-4.us-west-2.compute.amazonaws.com/

## About
This is the final project for the Udacity Full Stack Nano Degree. The requirements were to take a baseline installation of a Linux server and prepare it to host a web application. Secure the server from a number of attack vectors, install and configure a database server, and deploy an existing web application onto it.
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
3. $ chmod 600 ~/.ssh/udacity.pem - make the key secure.
4. $ ssh -i ~/.ssh/udacity.pem ubuntu@[public IP address shown on the Lightrail page] - log into the server
### Upgrade packages
1. $ sudo apt-get update
2. $ sudo apt-get upgrade
3. $ sudo apt-get install finger
### Configure the firewall
1. $ sudo nano /etc/ssh/sshd_config file - find the Port line and change 22 to 2200.
2. $ sudo service ssh restart - restart ssh
3. $ sudo ufw status - check for firewall
4. $ sudo ufw default deny incoming - block everything coming in.
5. $ sudo ufw default allow outgoing - allow everything outgoing.
6. $ sudo ufw allow 2200/tcp
7. $ sudo ufw allow 80/tcp
8. $ sudo ufw allow 123/udp
9. $ sudo ufw enable
### Create user grader
