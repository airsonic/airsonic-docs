---
layout: docs
title: Setting up Apache
permalink: /docs/proxy/apache/
---

The following configurations works for HTTPS (with an HTTP redirection).

> **NOTE**: Make sure you follow the [prerequisites](/docs/proxy/prerequisites/).

#### Apache configuration

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

    ProxyPass         /airsonic http://127.0.0.1:8080/airsonic
    ProxyPassReverse  /airsonic http://127.0.0.1:8080/airsonic
    RequestHeader     set       X-Forwarded-Proto "https"
</VirtualHost>
```

Alternatively, if you want to use an existing configuration, you can also paste
the configuration below inside an existing `VirtualHost` block:

```apache
ProxyPass         /airsonic http://127.0.0.1:8080/airsonic
ProxyPassReverse  /airsonic http://127.0.0.1:8080/airsonic
RequestHeader     set       X-Forwarded-Proto "https"
```

You will need to make a couple of changes in the configuration file:

- Replace `example.com` with your own domain name.
- Be sure to set the right path to your `cert.pem` and `key.pem` files.
- Change `/airsonic` following your Airsonic context path.
- Change `http://127.0.0.1:8080/airsonic` following you Airsonic server location, port and path.

Activate the host:

```
sudo a2ensite airsonic.conf
```

Activate the following Apache modules:

```
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod ssl
sudo a2enmod headers
```

Restart the `apache2` service:

```
sudo systemctl restart apache2.service
```

#### Content Security Policy

You may face some `Content-Security-Policy` issues. To fix this, add the following line to your Apache configuration:

```apache
<Location /airsonic>
    Header set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' www.gstatic.com; img-src 'self' *.akamaized.net; style-src 'self' 'unsafe-inline' fonts.googleapis.com; font-src 'self' fonts.gstatic.com; frame-src 'self'; object-src 'none'"
</Location>
```
