# How to Deploy Node App on Heroku VPS

## update System

#### sudo apt update
#### sudo apt install nginx
#### sudo apt install ufw


## enable ufw and ports 

#### sudo ufw status
#### sudo ufw allow ssh <--(Port 22)-->
#### sudo ufw allow http <--(Port 80)-->
#### sudo ufw allow https <--(Port 443)-->
#### ufw app list
#### sudo ufw allow 'Nginx HTTP'
#### sudo ufw enable

> check status is enabled or not

#### sudo /etc/init.d/apache2 stop
#### systemctl start nginx
#### sudo apt-get remove apache2*

#### mkdir -p /var/www/servername.com/html
#### chown -R $USER:$USER /var/www/servername.com/html
#### chmod -R 755 /var/www/servername.com
#### nano /var/www/servername.com/html/index.html

> Now paste sample html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nginx Server Test</title>
</head>
<body>
    <center>
        <h1> Hello Server is Running Properly </h1>
    </center>
</body>
</html>
```
> save the file


## setup nginx config


#### nano /etc/nginx/sites-available/servername.com

```
server { 
  listen 80; 
  listen [::]:80; 
  root /var/www/servername.com/html; 
  index index.html index.htm index.nginx-debian.html; 
  server_name [Your Server IP]; 
  location / { 
  try_files $uri $uri/ =404;
  }
}
```


#### ls /etc/nginx
#### ln -s /etc/nginx/sites-available/servername.com /etc/nginx/sites-enabled

#### nginx -t
#### systemctl restart nginx
#### systemctl status


## check your html page is working on not on server ip address

#### curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
#### . ~/.nvm/nvm.sh
#### nvm install node
#### node -v
#### npm -v
#### apt-get update -y
#### apt-get install git -y
#### git --version
#### npm install pm2 -g


## clone your repo

#### git clone [yourURL]
#### cd repo
#### npm install

#### pm2 start app.js -n [app name]

#### cd /etc/
#### cd nginx
#### nano sites-available/servername.com


## remove all server script and paste

```
server {
    listen 80;
    listen [::]:80;
    server_name domain.com www.domain.com;
    location / {
    proxy_pass http://localhost:8080;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    }
}
```

#### pm2 startup ubuntu
#### nginx -t
#### systemctl restart nginx

## connect domain and ip
> go to domain cpanel and add ip in @A Record
> check domain with http

## add ssl

#### sudo apt update
#### sudo apt-get install python3-certbot-nginx
#### sudo certbot --nginx


## non www to www redirection

#### nano /etc/nginx/sites-available/servername.com

> paste this in server after server name
 
```
if ($host = 'domain.com') {
      return 301 https://www.domain.com$request_uri;
}
```
> go down and find port 80 server replace  both

```
return 301 https://$host$request_uri;
    ->   With    ->   
return 301 https://www.domain.com$request_uri;
```


## enable http/2


>go down and find server port 433 and add http2 after 433 ssl on both lines
>like

```
listen [: :]:433 ssl https
listen 433 ssl https
```

## enable http strict transport security
> go down and find server port 433 and add this code after 301 or before location
#### add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";


## turn off server signature

#### nano /etc/apache2/apache2.conf
> paste below code in the file

#### ServerSignature Off
#### ServerTokens Prod

#### systemctl restart nginx