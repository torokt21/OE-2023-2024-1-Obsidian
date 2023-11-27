# 1. óra
`sudo su`
`cd`

LAMP szerver
Linux, apache, mySQL, Php


`docker container  ls` - jelenleg futó containerek
`docker container  ls -a` - összes container (ami áll az is)
`docker stop [container]` - container leállítása
`docker rm [container]` - container törlése

`docker run -d --rm --name web -p 80:80 -v /root/web/:/usr/local/apache2/htdocs/ httpd:2.4` - root web mappáját átmappelni a htdocsba

`docker exec -it [web] /bin/bash` - a containerbe belépni bash
exit - vissza a hostra

`docker run -d --name deb debian` - debian elindítása
Nem látjuk a futó containerek között, mert nem fut

`docker run -dit --name deb debian` - debian elindítása interaktívan, nem áll le indulás után

`docker container run --name db -e MYSQL_ROOT_PASSWORD=titok -e MYSQL_DATABASE=db1 -e MYSQL_USER=devops -e MYSQL_PASSWORD=nemtudom -d percona:5` - SQL adatbázis készítése (a portot magának forwardolja a 3306)
`docker run --name phpmyadmin -d --link db:db -p 80:80 nibrev/phpmyadmin-4.0.x` - phpmyadmin

`docker container run --restart=always -p 1111:80 --link db:db --name wordpress -d wordpress:6.0.0-apache` - wordpress telepítése a 1111 portra (ami a container 80as portjára fordul le) 

# 2. óra
nano Dockerfile - A dockerfile létrehozása, nincs kiterjesztés!

```
FROM php:apache-bookworm  
MAINTAINER Kovács András <kovacs.andras@nik.uni-obuda.hu>  
COPY ./index.php /var/www/html/index.php
```


`docker image build -t torokt21/todoman .` - az image létrehozása. a pont (.) kell, mert a jelenlegi mappából kell csinálni

`docker  login`
`docker image push torokt21/todomanager:latest`

SQL indítása
`docker run --restart=always -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Almafa123" -p 1433:1433 --name mssql --hostname mssql -d mcr.microsoft.com/mssql/server:2022-latest`

Server exporerben kell hozzáadni a szerver

Visual Studio > Tools > Connect to database

IP alapján működött  csak!
appsettings.json-be mehet a connection string
`Server=74.234.242.60;Database=photo;User Id=sa;Password=Almafa123`

Az imageben már
`Server=mssql;Database=hellomvc;User Id=sa;Password=Almafa123;`
mssql, mert azt adtuk meg az SQL indítása lépésnél


Action-ök beállítása githubon (secrettel)

`docker run -d --rm --name hellomvc -p 80:80 --link mssql:mssql torokt21/hellomvc`
