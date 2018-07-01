# Udacity Linux Server Cinfiguration Project

## Project Overview
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

## Why this Project?
A deep understanding of exactly what your web applications are doing, how they are hosted, and the interactions between multiple systems are what define you as a Full Stack Web Developer. In this project, you’ll be responsible for turning a brand-new, bare bones, Linux server into the secure and efficient web application host your applications need.

## Software needed for the server configuration ??
* You need to have a server instance of linux on any of the cloud based service providers and the only thing you need is a linux terminal to follow the instructions given below.

## What to do?
* Create an instance on either the Amazon EC2 or Google Cloud Compute.
* Connect to the instance using either terminal or Putty.
* To connect to my instance as grader please type `ssh grader@ec2-18-220-161-11.us-east-2.compute.amazonaws.com -i .ssh/grader_key.pem -p 2200` (the key is provided in Note to the reviewer. Please copy it to your '.ssh/grader_key.pem' file.).
* After connecting succesfully to your instance as admin please follow the following instructions to host your Flask Application on linux server.
    * To create 'grader' as a user type 
        `sudo adduser grader` in the terminal.
    * Then to provide permissions to run command as root user you need to provide sudo permission to the grader by typing `sudo nano /etc/sudoers.d/grader` and adding 'grader ALL=(ALL:ALL) ALL' to the file. Press ctrl + X to confirm and save.
    * After giving required permissions type `sudo su grader`.
    * Then create a directory .ssh using `mkdir .ssh`.
    * Change permissions to the directory by `chmod 700 .ssh`.
    * Create a file named authorized_keys in .ssh using `sudo nano .ssh/authorized_keys` and paste the public key content of the key generated from Amazon.
        * You can get the public key content by `ssh-keygen -y` and provide the path of your '.pem' file when prompted.
    * Now you have successfully created the grader user and you can connect to it via ssh the password login is diabled by default in Amazon EC2 instance.
    * To change the default port of ssh connection use `sudo nano /etc/ssh/sshd_config` and change the port to 2200. After changing the port use `sudo service sshd restart`.
    * To get your flask application up and running, first you need to configure the postgresql.
        * To install 'postgresql' use `sudo apt-get install postgresql`.
            * To login in postgresql use `sudo -u postgres psql` if it gives an error like (Is the server running locally and accepting connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?). Please start the postgresql service by using `sudo service postgresql start` before executing the above command.
            * Then to create a grader user of postgres database `create role grader;`.
            * `alter role grader superuser;`.
            * `alter role grader PASSWORD 'grader';`.
            * `ALTER ROLE "grader" WITH LOGIN;`.
            * Now create a database named catalog for the project using `create database catalog with owner = grader;`.
        * Open terminal and type the following command to install mod_wsgi:
            `sudo apt-get install libapache2-mod-wsgi python-dev`.
        * To enable mod_wsgi, run the following command: `sudo a2enmod wsgi `.
        * Move to the /var/www directory: `cd /var/www `.
        * Create a directory 'FlaskApp' using `sudo mkdir FlaskApp`.
        * Move inside FlaskApp directory `cd FlaskApp`.
        * Clone the catalg project from github into FlaskApp directory using
            `sudo git clone https://github.com/vikrant-kadian/catalog.git FlaskApp`.
        * Change 'project.py' file to __init__.py using `sudo mv FlaskApp/project.py FlaskApp/__init__.py`.
        * Move to newly FlaskApp directory inside FlaskApp directory `cd FlaskApp`.
        * We will use pip to install virtualenv and Flask so first install pip `sudo apt-get install python-pip`. If it gives some error try running `sudo apt-get update` and `sudo apt-get upgrade` before running the above command.
        * Install virtualenv using `sudo pip install virtualenv`.
        * Create a virtual environment named 'venv' using `sudo virtualenv venv`.
        * Activate the virtual environment using `source venv/bin/activate`.
        * Install the important libraries to run the __inint__.py file using:
            * `sudo pip install Flask`
            * `sudo pip install sqlalchemy`
            * `sudo pip install psycopg2`
            * `sudo pip install psycopg2-binary`
            * `sudo pip install requests`
            * `sudo pip install passlib`
            * `sudo pip install flask_httpauth`
            * `sudo pip install oauth2client`
        * Run the 'database_setup.py' python file using `sudo python database_setup.py`.
        * Run the 'add_data.py' python file using `sudo pytho add_data.py`.
        * Run the '__init__.py' python file using `sudo python __init__.py`.
        * It should display “Running on http://localhost:5000/” or "Running on http://127.0.0.1:5000/". If you see this message, you have successfully configured the app.
        * To deactivate the environment, give the following command: `deactivate`.
        * Now use the following commands for further setup for the application:
            * `sudo nano /etc/apache2/sites-available/FlaskApp.conf`
            * Add the content provided below in the 'FlaskApp.conf' file.
            ```
            <VirtualHost *:80>
		            ServerName http://18.220.161.11
		            ServerAdmin vikrantkadianindia@gmail.com
		            WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
		            <Directory /var/www/FlaskApp/FlaskApp/>
			            Order allow,deny
			            Allow from all
		            </Directory>
		            Alias /static /var/www/FlaskApp/FlaskApp/static
		            <Directory /var/www/FlaskApp/FlaskApp/static/>
			            Order allow,deny
			            Allow from all
		            </Directory>
		            ErrorLog ${APACHE_LOG_DIR}/error.log
		        LogLevel warn
		        CustomLog ${APACHE_LOG_DIR}/access.log combined
            </VirtualHost>
            ``` 
            * Enable the virtual host with the following command:
                `sudo a2ensite FlaskApp`.
            * `cd /var/www/FlaskApp`
            * `sudo nano flaskapp.wsgi`
                Add the following content to the above file:
                ```
                #!/usr/bin/python
                import sys
                import logging
                logging.basicConfig(stream=sys.stderr)
                sys.path.insert(0,"/var/www/FlaskApp/")

                from FlaskApp import app as application
                    application.secret_key = 'secret_key'
                ```
            * Restart Apache with the following command to apply the changes:
             `sudo service apache2 restart`

    * Visit http://18.220.161.11 for demo of the project.

## Reference 
    
* The [digital ocean tutorial.](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)  
* Udacity Linux Server Configuration Course.
