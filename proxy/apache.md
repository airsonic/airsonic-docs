---
layout: docs
title: Setting up Apache
permalink: /docs/proxy/apache/
---
The following configurations works for HTTPS (with an HTTP redirection).

Create a new virtual host file:

```
sudo nano /etc/apache2/sites-available/airsonic.conf
```

Paste the following configuration in the virtual host file:

```apache
<VirtualHost *:80>
   ServerName example.com
   Redirect permanent / https://example.com/
</VirtualHost>

<VirtualHost *:443>
    SSLEngine On
    SSLCertificateFile cert.pem
    SSLCertificateKeyFile key.pem
    SSLProxyEngine on
    ServerName example.com
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/airsonic-access.log combined
    ErrorLog ${APACHE_LOG_DIR}/airsonic-error.log
    ProxyPass /airsonic http://127.0.0.1:8080/airsonic
    ProxyPassReverse /airsonic http://127.0.0.1:8080/airsonic
    RequestHeader set X-Forwarded-Proto "https"
</VirtualHost>
```

You will need to make a couple of changes in the configuration file:
- Replace `example.com` with your own domain name.
- Be sure to set the right path to your `cert.pem` and `key.pem` files.
- Change `/airsonic` following your airsonic server path.
- Change `http://127.0.0.1:8080/airsonic` following you airsonic server location, port and path.
> **NOTE**:  you could only add ProxyPass and ProxyPassReverse lines to your existing configuration:
```apache
ProxyPass         /airsonic http://127.0.0.1:8080/airsonic
ProxyPassReverse  /airsonic http://127.0.0.1:8080/airsonic
```

Activate the host:

```
sudo a2ensite airsonic.conf
```

Activate apache2 proxy, proxy_http and ssl module:

```
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod ssl
```

Restart the Apache2 service:

```
sudo systemctl restart apache2.service
```
