1) Készíts a szerveren egy mappát valahova Pictures néven  
`sudo so`
`cd`
`mkdir Pictures`
2) Telepítsd a FileBrowser konténert (https://filebrowser.org/installation)  
3) Add át neki a Picture mappát /srv néven 
`docker run -d --name filebrowser -v /root/Pictures:/srv -e PUID=$(id -u) -e PGID=$(id -g) -p 8080:80 filebrowser/filebrowser:s6`
4) A Pictures mappára adj mindenkinek rw jogot (chmod 666)  
`chmod 666 Pictures`
5) A filebrowsert publikáld ki reverse proxyval a files.allat.bprof.hu címen, certificate legyen!  

Reverse proxy
`nano /etc/apache2/sites-available/files.conf`

```
Define domain files.bee.bprof.hu

Define port 8080

<VirtualHost *:80>

ServerName ${domain}

ServerAlias ${domain}

RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}

Protocols h2 http/1.1

ProxyPreserveHost On

ProxyPass / http://localhost:${port}/

ProxyPassReverse / http://localhost:${port}/

RewriteEngine On

RewriteCond %{HTTPS} off

RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI}

ErrorLog ${APACHE_LOG_DIR}_${domain}-error.log

CustomLog ${APACHE_LOG_DIR}_${domain}-access.log common

</VirtualHost>

<VirtualHost *:443>

Protocols h2 http/1.1

ProxyPreserveHost On

ProxyPass / http://127.0.0.1:${port}/

ProxyPassReverse / http://127.0.0.1:${port}/

ServerName ${domain}

ServerAlias ${domain}

ErrorLog ${APACHE_LOG_DIR}_${domain}-error.log

CustomLog ${APACHE_LOG_DIR}_${domain}-access.log common

SSLEngine on

SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1

SSLHonorCipherOrder off

SSLCompression off

SSLSessionTickets on

SSLUseStapling off

SSLCipherSuite ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECD$

SSLCertificateFile /etc/letsencrypt/live/${domain}/fullchain.pem

SSLCertificateKeyFile /etc/letsencrypt/live/${domain}/privkey.pem

</VirtualHost>
```

`a2ensite files`
`systemctl reload apache2`


Certificate:
`systemctl stop apache2.service`

```
certbot certonly --preferred-challenges http -d files.bee.bprof.hu --agree-tos --manual-public-ip-logging-ok --register-unsafely-without-email --rsa-key-size 4096
```

`systemctl start apache2.service`

6) Itt lehessen képeket feltölteni  
4) Csinálj egy nagyon egyszerű ASP.NET MVC alkalmazást, amely megnézi, hogy a wwwroot-ban milyen jpg fájlok vannak, ezeket listaként tudja átadni a view-nak  
5) Engedélyezd a static file hostingot (alapvetően a /wwwroot-ból szolgál ki)  
6) Csinálj egy view-t, ami megjeleníti a képeket pl. bootstrap cardokon  
7) A konténert deployold, csatold bele a pictures mappát a wwwroot helyére  
8) publikáld kifele photos.allat.bprof.hu címen certificate-el!