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
4. Choose a payment plan - for this project pick the lowest one to free-tier access.
5. Give the instance a unique name and click 'Create'.
6. Wait for the instance to start up.
### Connect to the instance on a local machine
1. Navigate to the Account Page and download the default key.
2. Save this in ~/.ssh with the name udacity.pem.
3. To make the key secure, from terminal input chmod 600 ~/.ssh/udacity.pem.
4. Log in: ssh -i ~/.ssh /udacity.pem ubuntu@[public IP address shown on the Lightrail page]
