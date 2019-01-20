## OS Prerequiste
This case refers to "Linux - Ubuntu 18LTS".

Insall docker and docker-compose.
``` bash
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install docker.io
$ sudo apt install docker-compose
```

Create "docker" user and also add your current user to docker group. This will make your login user able to execute "docker" and "docker-compose" commands.
``` bash
$ sudo adduser --uid 116 --disabled-login --ingroup docker docker -q
$ sudo usermod -a -G docker $USER
$ sudo chfn -o umask=007 docker
$ sudo chmod -R g+w .
``` 

Clone "docker-redmine" project from GitHub.
``` bash
git clone https://github.com/winain7788/docker-redmine.git
cd docker-redmine/
```

## Network and Domain
In this case, we will use [Let's Encrypt SSL] (https://letsencrypt.org/getting-started/). You will need to configure your domain name properly to proceed further steps.

1) Add your domain to your DNS service, for example - redmine.yourcompany.com. You will also need to add www.redmine.yourcompany.com to your DNS configuration too.
2) Verify your configuration using "nslookup".
``` bash
$ nslookup redmine.yourcompany.com
$ nslookup www.redmine.yourcompany.com
```

3) Now you are ready to generate your SSL certificate for your server. Execute the command below.
``` bash
$ ./letsencrypt/letsencrypt-init.sh redmine.devteller.com
```


vi nginx/proxy_ssl.conf_example 
docker-compose ps

cp proxy_ssl.conf_example proxy_ssl.conf
cd ..
ls
docker-compose up -d
docker-compose ps

docker exec -it mariadb bash
vi docker-compose.yml 
ls -lrt
cd mysql/
ls
ls -lrt
cd ..
docker logs mariadb
ls
cd mysql/
ls
cd ..
ls
vi docker-compose.yml 
docker-compose stop
sudo rm -fr ./mysql/*
ls -l
docker image ls
docker-compose up -d
docker-compose ps
docker-compose stop
cd ../docker/docker-redmine/mysql/
ls
vi backup.sql 
exit
cd ../docker/
ls
cd docker-redmine/
ls
docker-compose up -d
docker-compose ps
docker-compose logs mariadb
docker-compose logs redmine
ls
sudo mv backup.sql mysql/
cd mysql/
ls -l
cd ..
sudo mv backup-migrate.sql mysql/
docker exec -it mariadb bash

cp configuration.yml_example configuration.yml


# Clean up
docker container rm $(docker container ls -a -q)
docker image prune

