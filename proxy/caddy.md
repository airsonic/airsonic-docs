---
layout: docs
title: Setting up Caddy
permalink: /docs/proxy/caddy/
---

The following configuration works for HTTPS (with an HTTP redirection).

> **NOTE**: Make sure you follow the [prerequisites](/docs/proxy/prerequisites/).

#### Caddy configuration

Create a new virtual host file (assumes `/etc/caddy/caddy.conf` is your main file, and includes all `*.conf` files in `/etc/caddy/caddy.conf.d/` directory):

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

Check the Caddy config for validity, and then restart the Caddy service:

```
caddy -conf /etc/caddy/caddy.conf -validate
sudo systemctl restart caddy.service
```
