# lunix-deploy
udacity full stack linux deployment project

## The project's Information:
articlehive.be
ec2-3-122-17-53.eu-central-1.compute.amazonaws.com
#### ip:
3.122.17.53

## Steps to deplying our server:
1- Create Amazon Lightsail acount then an instance. The one withthe free tier would suffice
2- configure our server's ufw :
``` 
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow http
sudo ufw allow 123/udp
```
-- edit the sshd.config file:
add port 2200 along with the default port 20 (to be deleted after we check that our connection using the custom port has passed)
-- to allow the custom port for ssh, we must log to Amazon's EC2 console related to our Lightsail account. Then, from within the console left menu tabs, click NETWORK & SECURITY -> security groups. Select inbound -> edit, then, add custom rules: udp port 123, custom tcp port
2200, http 
3- create user grader with `sudo adduser grader` 
4- give sudo access to the grader
-- we will need to log in as grader to place public_key so we will need to update the users configuration files to allow access with password. now connect using `ssh grader@3.122.17.53 -p 2200` 
5- generate ssh key for grader and name it grader. place this key in your local .ssh folder in your local machines home dir.
6- upload grader key to amazon lightsail instance, networking tab.
7- connect to the 
