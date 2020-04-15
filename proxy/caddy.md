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

#### Forward headers

You will also need to make sure Airsonic uses the correct headers for redirects, by setting the `server.use-forward-headers` property to `true`.

To do so, stop your Airsonic server or Docker image, then edit the `config/application.properties` file:

```
nano /path/to/airsonic/config/airsonic.properties
```

Add the following line to the bottom of the file:
```
server.use-forward-headers=true
```

Use Ctrl+X to save and exit the file, and restart your Airsonic server or Docker image.
