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

28.	Run ``sudo apt-get install libapache2-mod-wsgi```

29.	Run ```sudo apt-get install postgresql``
