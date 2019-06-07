---
layout: docs
title: Setting up Caddy
permalink: /docs/proxy/caddy/
---
The following configuration works for HTTPS (with an HTTP redirection).

Create a new virtual host file (assumes /etc/caddy/caddy.conf is your main file, and includes all *.conf files in /etc/caddy/caddy.conf.d/ directory):

```
sudo nano /etc/caddy/caddy.conf.d/airsonic.conf
```

Paste the following configuration in the virtual host file:

```caddy
airsonic.mydomain.com {
    proxy / 127.0.0.1:8080 {
        transparent
    }
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

Check the Caddy config for validity, and then restart the Caddy service:

```
caddy -conf /etc/caddy/caddy.conf -validate
sudo systemctl restart caddy.service
```
