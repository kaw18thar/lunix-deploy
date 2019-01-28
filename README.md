# lunix-deploy
udacity full stack linux deployment project

## The project's Information:
[articlehive.be](http://articlehive.be)
#### ip:
3.122.17.53

## Steps to deplying our server:
1. Create Amazon Lightsail acount then an instance. The one withthe free tier would suffice
2. configure our server's ufw :
``` 
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow http
sudo ufw allow 123/udp
```
-- edit the sshd.config file:
add port 2200 along with the default port 20 (to be deleted after we check that our connection using the custom port has succeeded)
-- to allow the custom port for ssh, we must log to Amazon's EC2 console related to our Lightsail account. Then, from within the console left menu tabs, click NETWORK & SECURITY -> security groups. Select inbound -> edit, then, add custom rules: udp port 123, custom tcp port
2200, http 
-- enable the ufw using `sudo ufw enable`
3. change the server time zone to UTC:
`sudo dpkg-reconfigure tzdata`
choose the last option to go to the second menu and choose utc
4. create user grader with `sudo adduser grader` 
5. give sudo access to the grader
-- we will need to log in as grader to place public_key so we will need to update the users configuration files to allow access with password. now make another terminal connect using `ssh grader@3.122.17.53 -p 2200`  and enter password of grader
6. generate ssh key locally for grader and name it grader. place this key in your local .ssh folder in your local machines home dir.
7. upload grader key to amazon lightsail instance, networking tab. 
8. in grader home and while logged in as grader create a new directory and name it `.ssh`. inside .ssh directory create directory `authorized_keys` direvctory ree. then, using `cat ~/.ssh/grader.pub` copy the contents of the grader public key and use the command `sudo nano /.ssh/authorized_keys/grader.pub` to place it in `/.ssh/authorized_keys` directory.
9. connect to the grader account using `ssh grader@3.122.17.53 -p 2200 -i ~/.ssh/grader`. The logging in should be successful now, so, we need to configure sshd.config file to delete the `port 20` and leave `port 2200` there.
10. upgrade the lunux server packages using 
``` 
sudo apt-get update
sudo apt-get install apache2
```
11. install apache server using the commands 
```
sudo apt-get update
sudo apt-get install apache2
```
12. install then enabel model wsgi using :
```sudo apt-get install libapache2-mod-wsgi python-dev
sudo a2enmod wsgi 
```
13.create a flask app :
- `cd /var/www/`
- `sudo mkdir catalog`
- `cd catalog`
-  clone the catalog project using ` git clone https://github.com/kaw18thar/article_hive.git catalog` this would make a directory called catalog inside the parent catalog directory 
-  we update our app's credentials in console:
* add articlehive.be in authorized javascript origins
 and 	http://articlehive.be/login	
http://articlehive.be/gconnect in Authorized redirect URIs. Download the client secret in your computer and open it, copy all the texts and then `cd catalog` `sudo nano client_secrets.json`. paste the text from the newly downloaded client secret file and save the file.
- `mv finalproject.py __init__.py`
14. configure our wsgi app
- `sudo apt-get install python-pip `
- `sudo pip install virtualenv `
- `sudo pip install virtualenv `
- `sudo virtualenv venv`
- `sudo nano /etc/apache2/sites-available/catalog.conf`
- paste : 
```<VirtualHost *:80>
		ServerName articlehive.be
		ServerAdmin admin@articlehive.be
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
- `sudo nano /etc/apache2/sites-enabled/000-default.conf`
 add the following line before the end of virtualhost tag:
`WSGIScriptAlias / /var/www/catalog/catalog.wsgi`
- create wsgi file in  `/var/www/catalog` using:
`sudo nano catalog.wsgi `
add code:
```import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'
```
15. install flask and packages used in our application: 

- `sudo pip install flask`
- 
``` sudo pip install sqlalechemy 
sudo pip install requests
sudo pip install oauth2client
sudo pip install psycopg2
```
16. install postgresql:
```sudo apt-get install libpq-dev python-dev
sudo apt-get install postgresql postgresql-contrib
```
17. create catalog table in postgresql
```sudo -u postgres psql
postgres=# create database catalog;
postgres=# create user catalog with encrypted password 'password';
postgres=# grant all privileges on database catalog to catalog;
```
18. `sudo nano /catalog/__init__.py`:
- modify the clientt secret path to `/var/www/catalog/catalog/client_secrets.json` in both lines 22 and 57
-  modify the database connection line 25-26 to 
```engine = create_engine(
    'postgresql://catalog:password@localhost/catalog')
```
19. `sudo nano /catalog/database_setup.py` 
- update line 100 to be `'postgresql://catalog:password@localhost/catalog')`
20. `sudo nano /catalog/createcollections.py'
- update line 9 to
`engine = create_engine('postgresql://catalog:password@localhost/catalog')`
21. `sudo service apache2 restart` 

the application is now alive at: [articlehive.be](http://articlehive.be)

## Special thanks to the writers of these two articles:
- [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

- [Creating user, database and adding access on PostgreSQL](https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e)
