Udacity Full Stack Web Development
Linux Server Congfiguration Project
Student: Robert "Zach" Coffee
___________________________________________________________________

Udacity Project Overview:
You will take a baseline installation of a Linux server and prepare it to host your web applications.
You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.
___________________________________________________________________

Amazon Lightsail Virtual Machine Details:
Public IP:18.188.228.241
SSH Port: 2200
Web Application URL: http://ec2-18-218-1-37.us-east-2.compute.amazonaws.com

*** Note: Please use the web application URL to launch the Item Catalog application
___________________________________________________________________

Steps for Configuration:

1) Set up virtual machine Instance within Amazon Lightsail

2) SSH into virtual machine instance using private key:
  - Download Private Key to local machine and save to ~/downloads/udacity_key.pem
  - Move the private key to local ~/.ssh directory $ mv ~/downloads/udacity_key.pem ~/.ssh
  - Set up file permissions $ chmod 600 ~/.ssh/udacity_key.pem
  - SSH into virtual machine $ ssh -i ~/.ssh/udacity_key.pem grader@18.188.228.241

3) Create new user & give sudo permissions:
  - $ sudo adduser grader
  - Ensure new user was created $ sudo apt-get install finger, then $ finger grader to see user details
  - $ mkdir /etc/sudoers.d
  - $ touch /etc/sudoers.d/grader
  - $ nano /etc/sudoers.d/grader, add ALL=(ALL:ALL) NOPASSWD: ALL , then save (CTRL+X) & exit

4) SSH as grader into virtual machine using public key:
  - $ ssh-keygen and save public key to local ~/.ssh directory

  - $ su - grader
  - $ mkdir .ssh
  - $ touch .ssh/authorized_keys
  - $ nano .ssh/authorized_keys
  - Copy & Paste public key you created on local machine, then save (CTRL+X) & exit
  - Set file permissions
    - $ chmod 700 .ssh
    - $ chmod 644 .ssh/authorized_keys
  - $ sudo service ssh restart
  - log into grader $ ssh grader@18.188.228.241 -p 2200 -i ~.ssh/linuxcourse.pub

5) Change port from 22 to 2200:
  - $ sudo nano /etc/ssh/sshd_config
  - Change Port 22 to Port 2200, then save (CTRL+X) & exit

6) Update all packages:
  - sudo apt-get install update
  - sudo apt-get install upgrade

7) Enable firewall and enable permissions:
  - $ sudo ufw allow 2200/tcp
  - $ sudo ufw allow 80/tcp
  - $ sudo ufw allow 123/udp
  - $ sudo ufw enable
  - To check firewall status $ sudo ufw status
  - $ sudo restart ssh service

8) Disable SSH login for root
  - $ sudo nano /etc/ssh/sshd_config
  - Scroll down to PermitRootLogin and change to 'no'
  - $ sudo service ssh restart

9) Installing / configure mod_wsgi:
  - sudo apt-get install apache2
  - sudo apt-get install libapache2-mod-wsgi
  - sudo service apache2 restart

10) Install / configure PostgreSQL:
  - sudo apt-get install postgresql
  - sudo touch /etc/postgresql/9.3/main/pg_hba.conf
  - sudo su - postgres
  - pqsl
  - postgres=# CREATE DATABASE catalog;
  - postgres=# CREATE USER catalog;
  - postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
  - postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
  - exit postgres=# \q & exit

11) Create / configure .wsgi file:
  - In /var/www/FlaskApp: sudo touch flaskapp.mod_wsgi
  - Add the following code:
        #!/usr/bin/python
      import sys
      import logging
      logging.basicConfig(stream=sys.stderr)
      sys.path.insert(0,"/var/www/FlaskApp/")

      from FlaskApp import app as application
      application.secret_key = 'super_secret_key'

12) Google Oauth changes:
  - Update Authorized JavaScript Origins & redirect URIs
  - Download and copy/paste update client_secrets.JSON within FlaskApp
  - Update Cliet_ID & Oath_Flow within the __init__py file

13) sudo service apache2 restart


___________________________________________________________________

Resources:
1)https://httpd.apache.org/docs/2.2/configuring.html
2)https://askubuntu.com/questions/256013/apache-error-could-not-reliably-determine-the-servers-fully-qualified-domain-n
3)http://wsgi.readthedocs.io/en/latest/
4)http://flask.pocoo.org
5)https://docs.djangoproject.com/en/1.8/howto/deployment/wsgi/
6) https://stackoverflow.com/questions/36020374/google-permission-denied-to-generate-login-hint-for-target-domain-not-on-localh
