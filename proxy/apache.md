---
layout: docs
title: Setting up Apache
permalink: /docs/proxy/apache/
---
The following configurations works for HTTPS.
> Note that SSL certificates are available for free from [Let's Encrypt](https://letsencrypt.org).

Create a new virtual host file:

```
sudo nano /etc/apache2/sites-available/airsonic.conf
```

Paste the following configuration in the virtual host file:

```apache
<VirtualHost *:80>
   ServerName music.example.com
   Redirect permanent / https://music.example.com/
</VirtualHost>

<VirtualHost *:443>
    SSLEngine On
    SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
    SSLProxyEngine on
    ServerName music.example.com
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/airsonic-access.log combined
    ErrorLog ${APACHE_LOG_DIR}/airsonic-error.log
    ProxyPass /airsonic http://127.0.0.1:8080/airsonic
    ProxyPassReverse /airsonic http://127.0.0.1:8080/airsonic
    RequestHeader set X-Forwarded-Proto "http"
</VirtualHost>

```

You will need to make a couple of changes in the configuration file:
- Replace `example.com` with your own domain name.
- Change `/etc/letsencrypt/live/example.com/fullchain.pem` with the correct certificate chain.
- Change `SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem` with the correct private key.
- Change `/airsonic` following your airsonic server path.
- Change `http://127.0.0.1:8080/airsonic` following you airsonic server location, port and path.
> Note that you could only add ProxyPass and ProxyPassReverse lines to your existing configuration:
```apache
ProxyPass         /airsonic http://127.0.0.1:8080/airsonic
ProxyPassReverse  /airsonic http://127.0.0.1:8080/airsonic
```

Activate the host:

```
sudo a2ensite airsonic.conf
```

Activate apache2 proxy and proxy_http module:

```
sudo a2enmod proxy
sudo a2enmod proxy_http
```

Restart the Apache2 service:

```
sudo systemctl restart apache2.service
```

<br>
##### Serve Airsonic under it's own domain with Tomcat #####

If you use Tomcat and want to serve your Airsonic instance under it's own domain (e.g. music.example.com), some changes to Tomcat are necessary.<br>
Simply modifying your virtual host file won't work as you won't be able to log in:
```apache
# WON'T WORK
ProxyPass         / http://127.0.0.1:8080/airsonic
ProxyPassReverse  / http://127.0.0.1:8080/airsonic
```

```apache
# WORKS
ProxyPass         / http://127.0.0.1:8080/
ProxyPassReverse  / http://127.0.0.1:8080/
```
<br>
In order for this to work Airsonic has to be served as root path by Tomcat.<br>
Modify server.xml (If you have not modified Tomcat defaults, it's under /var/lib/tomcat8/conf/).<br>
Add the following right above the `</Host>` tag:
```xml
<Context path="" docBase="libresonic" debug="0" reloadable="true">
  <WatchedResource>WEB-INF/web.xml</WatchedResource>
</Context>
<Context path="ROOT" docBase="ROOT"> <!-- Default set of monitored resources -->
  <WatchedResource>WEB-INF/web.xml</WatchedResource>
</Context>
```
<br>
Be sure to reload Apache:
```
sudo systemctl reload apache2.service
```

And restart Tomcat:
```
sudo systemctl restart tomcat8.service
```
