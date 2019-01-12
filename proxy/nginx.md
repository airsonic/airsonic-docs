---
layout: docs
title: Setting up Nginx
permalink: /docs/proxy/nginx/
---
The following configurations works for HTTPS (with an HTTP redirection).

Create a new virtual host file:

```
sudo nano /etc/nginx/sites-available/airsonic
```

Paste the following configuration in the virtual host file:

```nginx
# Redirect HTTP to HTTPS
server {
    listen      80;
    server_name example.com;
    return      301 https://$server_name$request_uri;
}

server {

    # Setup HTTPS certificates
    listen       443 default ssl;
    server_name  example.com;
    ssl_certificate      cert.pem;
    ssl_certificate_key  key.pem;

    # Proxy to the Airsonic server
    location /airsonic {
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header X-Forwarded-Host  $http_host;
        proxy_set_header Host              $http_host;
        proxy_max_temp_file_size           0;
        proxy_pass                         http://127.0.0.1:8080;
        proxy_redirect                     http:// https://;
    }
}
```

You will need to make a couple of changes in the configuration file:
- Replace `example.com` with your own domain name.
- Be sure to set the right path to your `cert.pem` and `key.pem` files.
- Change `/airsonic` following your airsonic server path.
- Change `http://127.0.0.1:8080` following you airsonic server location and port.
> **NOTE**:  you could only add the "location /airsonic" section to your existing configuration:
```nginx
# Proxy to the Airsonic server
location /airsonic {
    proxy_set_header X-Real-IP         $remote_addr;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header X-Forwarded-Host  $http_host;
    proxy_set_header Host              $http_host;
    proxy_max_temp_file_size           0;
    proxy_pass                         http://127.0.0.1:8080;
    proxy_redirect                     http:// https://;
}
```
You will also need to make sure Tomcat uses the correct headers for redirects. Stop your Airsonic server or docker image and:
```
nano /path/to/airsonic/config/airsonic.properties
```
Add the following line to the bottom of the file:
```
server.use-forward-headers=true
```
Ctrl+X to save and exit the file, and restart your Airsonic server or docker image.

> **NOTE**:  you may face some `Content-Security-Policy` issues. To fix this add the following line to your configuration:
```nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' www.gstatic.com; img-src 'self' *.akamaized.net; style-src 'self' 'unsafe-inline' fonts.googleapis.com; font-src 'self' fonts.gstatic.com; frame-src 'self'; object-src 'none'";
```

Activate the host by creating a symbolic link between the sites-available directory and the sites-enabled directory:

```
sudo ln -s /etc/nginx/sites-available/airsonic /etc/nginx/sites-enabled/airsonic
```

Restart the Nginx service:

```
sudo systemctl restart nginx.service
```
