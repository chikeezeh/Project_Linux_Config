# Overview
A baseline Linux server is configured to serve a web app built with Flask.
The server was updated and secured and also configured to serve data from a Postgresql database.


# Configuration

The web server was configured with these steps;

1. Set up a new remote Linux instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/)

2. Create a Static IP address 18.219.29.211

3. Downloaded the private key file from Amazon as a ```*pem``` file.

4. Generated the Private key in Puttygen and saved it as a ```*ppk``` file.

5. Loaded the ```*ppk``` private keys to Putty and configured it to allow remote connection to the Amazon Linux instance.

6. Saved my session on putty to auto login

7.	Run ```sudo apt-get update``` to list the required updates.

8.	Run ```sudo apt-get upgrade``` to upgrade the software.

9.	Create new user ```grader``` default password ```grader``` using ```sudo adduser grader```

10.	Create a ```sudoers.d``` for grader using ```sudo touch etc/sudoers.d/grader```

11.	Edit with nano ```sudo nano etc/sudoers.d/grader```

12.	Add ```grader ALL = (ALL) NOPASSWD:ALL``` into the file, this gives the grader user sudo access.

13. Make a ```.ssh``` folder inside the home directory of user grader, ``mkdir .ssh```

14. cd into ```.ssh``` and create a ```authorized_keys``` file using ```sudo touch authorized_keys```

15. Copy the content of ```authorized_keys``` in the ```ubuntu``` user and paste into ```authorized_keys``` in ```grader``` user, also make sure ```.ssh``` and ```authorized_keys``` owner and group is grader. This allows the grader to login with ssh.

16. Change file permissions on ```.ssh``` and ```.ssh/authorized_keys``` in grader user using chmod 700 and 644 respectively.

17. cd to ```etc/ssh```, then use ```sudo nano sshd_config``` to change ssh port from 20 to 2200.

18. Deny incoming request with ```sudo ufw default deny incoming```

19.	Allow outgoing connection with ```sudo ufw default allow outgoing```

20.	Change ssh from 22 to 2200 with ```sudo ufw allow 2200/tcp```

21.	Use ```sudo ufw allow www``` to set HTTP to port 80

22.	Use ```sudo ufw allow 123/tcp``` to set NTP to port 123

23.	```sudo ufw enable``` to enable the firewall

24.	Add custom TCP in the Amazon Lightsail account with TCP 2200

25.	Restart linux instance and change port on putty to 2200, then log in again.

26.	Install Apache with ```sudo apt-get install apache2```

27.	Go to 18.219.29.211 to check if apache is working

28.	Run ```sudo apt-get install libapache2-mod-wsgi```

29.	Run ```sudo apt-get install postgresql```

30. Enabled automatic update with ```sudo dpkg-reconfigure --priority=low unattended-upgrades```

# Configuring the database

1. run ```sudo su postgres``` to switch to the postgres user.

2. type ```psql``` to change to the Postgresql terminal.

3. create a new database called catalog with ``` CREATE DATABASE catalog;```

4. create user ```CREATE USER catalog WITH PASSWORD 'catalog';```

5. grant privileges with ```GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;```

# Configuring Apache2
1. installed mod-wsgi with ```sudo apt-get install libapache2-mod-wsgi```

2. edited ```/etc/apache2/sites-enabled/000-default.conf``` file by adding ```WSGIScriptAlias / /var/www/FlaskApp2/flaskapp2.wsgi``` before the ending ```<VirtualHost *:80>``` block.

3. created a FlaskApp2.conf file with ```sudo nano /etc/apache2/sites-available/FlaskApp.conf``` and added the following block of code to it;
```HTML
  <VirtualHost *:80>
          ServerName 18.219.29.211
          ServerAdmin ezeh.chike
          WSGIScriptAlias / /var/www/FlaskApp2/flaskapp2.wsgi
          <Directory /var/www/FlaskApp2/FlaskApp2/>
                  Order allow,deny
                  Allow from all
          </Directory>
          Alias /static /var/www/FlaskApp2/FlaskApp2/static
          <Directory /var/www/FlaskApp2/FlaskApp2/static/>
                  Order allow,deny
                  Allow from all
          </Directory>
          ErrorLog ${APACHE_LOG_DIR}/error.log
          LogLevel warn
          CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
```
4. resarted apache2 with ```sudo apache2ctl restart```

# Deploying the Flask app

1. installed git with ```sudo apt-get install git```

2. intalled python and all the app dependencies.

3. cloned the catalog app with ```git clone https://github.com/chikeezeh/Project_Item_Catalog.git ```

4. checked out the linux branch from the repo with ```sudo git checkout linux```

5. Changed the ownership of all the files in the project with ```chown grader:grader * ```

6. arranged my folder structure as shown in this [article](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

7. run ```python database_setup.py``` to create the database

8. run ```python populate_database.py``` to populate the database

9. put the following code in the flaskapp2.wsgi file ;
```python
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/FlaskApp2/")

from FlaskApp2 import app as application
application.secret_key = 'Add your secret key'
```

10. restart apache and go to 18.219.29.211 to launch the app.

# Resources
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

https://suhas.org/sqlalchemy-tutorial/

https://www.postgresql.org/docs/9.1/static/sql-grant.html
