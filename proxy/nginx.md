---
layout: docs
title: Setting up Nginx
permalink: /docs/proxy/nginx/
---

The following configurations works for HTTPS (with an HTTP redirection).

> **NOTE**: Make sure you follow the [prerequisites](/docs/proxy/prerequisites/).

#### Nginx configuration

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

Alternatively, if you want to use an existing configuration, you can also paste the
`location` block below inside your existing `server` block:

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

You will need to make a couple of changes in the configuration file:

- Replace `example.com` with your own domain name.
- Be sure to set the right path to your `cert.pem` and `key.pem` files.
- Change `/airsonic` following your Airsonic context path (`/` by default).
- Change `http://127.0.0.1:8080` following you Airsonic server location and port.

Activate the host by creating a symbolic link between the sites-available directory and the sites-enabled directory:

```
sudo ln -s /etc/nginx/sites-available/airsonic /etc/nginx/sites-enabled/airsonic
```

Restart the Nginx service:

```
sudo systemctl restart nginx.service
```

#### Forward headers

You will also need to make sure Airsonic uses the correct headers for redirects, by setting the `server.use-forward-headers` property to `true`.

To do so, stop your Airsonic server or Docker image, then edit the `config/application.properties` file:

```
nano /path/to/airsonic/config/application.properties
```

Add the following line to the bottom of the file:
```
server.use-forward-headers=true
```

Use Ctrl+X to save and exit the file, and restart your Airsonic server or Docker image.

#### Content Security Policy

You may face some `Content-Security-Policy` issues. To fix this, add the following line to your Nginx configuration:

```nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' www.gstatic.com; img-src 'self' *.akamaized.net; style-src 'self' 'unsafe-inline' fonts.googleapis.com; font-src 'self' fonts.gstatic.com; frame-src 'self'; object-src 'none'";
```
