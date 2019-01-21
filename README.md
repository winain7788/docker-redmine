# Installation Guideline
## OS Prerequiste Tasks
This case refers to "Linux - Ubuntu 18LTS".

1) Install docker and docker-compose. The command will be different if you use another Linux distribution.
``` bash
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install docker.io docker-compose
```

2) Create "docker" user and also add your current user to docker group. This will make your login user able to execute "docker" and "docker-compose" commands.
``` bash
$ sudo adduser --uid 116 --disabled-login --ingroup docker docker -q
$ sudo usermod -a -G docker $USER
$ sudo chfn -o umask=007 docker
$ sudo chmod -R g+w /home/docker
``` 

3) Clone "docker-redmine" project from GitHub.
``` bash
cd /home/docker
git clone https://github.com/winain7788/docker-redmine.git
cd docker-redmine/
```

## Network and Domain Configurations
In this case, we will use [Let's Encrypt SSL](https://letsencrypt.org/getting-started/). You will need to configure your domain name properly to proceed further steps.

1) Add your domain to your DNS service, for example - "redmine.yourcompany.com". You will also need to add "www.redmine.yourcompany.com" to your DNS configuration, otherwise certbot will fail to verify your domain name.
2) Verify your configuration using "nslookup".
``` bash
$ nslookup redmine.yourcompany.com
$ nslookup www.redmine.yourcompany.com
```
## Edit docker-compose.yml File
This step will tell you how to initialise docker-compose.yml file to specify your database password and your customised configurations e.g. email server.
1) Open docker-compose.yml file using your prefer editor and specify your prefer password for database.
``` bash
line 11      - REDMINE_DB_PASSWORD=your-password
line 23      - MYSQL_ROOT_PASSWORD=your-password
```
2) Create Redmine configuration file from the template.
``` bash
$ cd redmin/config
$ cp configuration.yml.example configuration.yml
```
3) You may configure outgoing emails server for your Redmine server. In this case, we use mailgun for example as below.
``` bash
# default configuration options for all environments
default:
  # Outgoing emails configuration
  # See the examples below and the Rails guide for more configuration options:
  # http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-configuration
  email_delivery:
    delivery_method: :smtp
    smtp_settings:

      address: "smtp.mailgun.org"
      port: 2525
      domain: 'yourcompany.com'
      authentication: :login
      user_name: 'postmaster@yourcompany
      password: 'your-mail-gun-key'
```

## SSL Certificates
1) Now you are ready to generate your SSL certificate for your server. Execute the command below.
``` bash
$ ./letsencrypt/letsencrypt-init.sh redmine.yourcompany.com
```
2) You will need to enter your email address as your will recieve a notification when your certificate is nearly expired. Then you agree with the terms of serve and you may say no to receive any further email.
``` bash
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): your email

-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: A

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: N
```
3) The you will see this message, which you will need to copy the text to update your webserver configuration files.
``` bash
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for redmine.yourcompany.com
http-01 challenge for www.redmine.yourcompany.com
Using the webroot path /data/letsencrypt for all unmatched domains.
Waiting for verification...
Cleaning up challenges
...
INFO: update the nginx/proxy_ssl.conf file
-  4:   server_name example.com;
- 19:   server_name               yourcompany.com www.yourcompany.com;
- 40:   ssl_certificate           /etc/letsencrypt/live/yourcompany.com/fullchain.pem;
- 41:   ssl_certificate_key       /etc/letsencrypt/live/yourcompany.com/privkey.pem;
- 42:   ssl_trusted_certificate   /etc/letsencrypt/live/yourcompany.com/chain.pem;
```

## Webserver Configurations
1) You will need to create a webserver configuration file by copying from the template.
``` bash
$ cd nginx
$ cp proxy_ssl.conf_example proxy_ssl.conf
```
2) Then edit the webserver configuration file - "proxy_ssl.conf" using any editor. Replace the server name and key files using the information from step 3 in SSL section. You will need to replace the value in line 60, 81, 82 and 83 using the same information from line 19, 40, 41 and 42.
3) Now your Redmine configuration should be ready to start using the commands below.
``` bash
$ docker-compose stop
$ docker-compose up -d
$ docker-compose ps
```
If you need a fresh Redmine installation, you are done now. However, you will need to proceed one more step below if you need to migrate your data from another Redmine instance.

## Database Migration
1) Backup your data from previous Redmine instance. Please refer to [Redmine Wiki](https://www.redmine.org/projects/redmine/wiki/RedmineBackupRestore) for details.
2) Once you have your backup in hands. In this case, we use filename  "backup-migrate.sql". Put your "backup-migrate.sql" in "docker-redmine/mysql" folder then you are now ready to proceed migration.
3) Enter the shell of database and execute the commands below. You will need to enter your database password from "Edit docker-compose.yml File" section.
``` bash
# docker exec -it mariadb bash
# cd /var/lib/mysql
# mysql -u root -p -D redmine < backup-migrate.sql
# exit
``` 
3) Stop and start Redmine service again.
``` bash
$ docker-compose stop
$ docker-compose up -d
$ docker-compose ps
```

## Service Access
1) Redmine Service URL
``` bash
https://redmine.yourcompany.com
```
2) Database Admin URL
``` bash
https://redmine.yourcompany.com:8443
```
3) Make sure that you have configure your firewall properly to access these services (port 80, 443 and 8443).

