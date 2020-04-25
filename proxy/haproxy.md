---
layout: docs
title: Setting up HAproxy
permalink: /docs/proxy/haproxy/
---

The following configurations works for HTTPS (with an HTTP redirection).

> **NOTE**: Make sure you follow the [prerequisites](/docs/proxy/prerequisites/).

#### HAProxy configuration

Open the HAProxy configuration file:

```
sudo nano /etc/haproxy/haproxy.cfg
```

Add these lines in your default section:

```haproxy
default

    # Use HTTP protocol
    mode http
```

Add these lines to the end:

```haproxy
frontend https

    # Listen on the HTTPS and HTTP ports
    bind :80
    bind :443 ssl crt /etc/haproxy/certs/cert_key.pem

    # Add X-Headers necessary for HTTPS; include:[port] if not running on port 443
    http-request set-header X-Forwarded-Host %[req.hdr(Host)]
    http-request set-header X-Forwarded-Proto https

    # (OPTIONAL) Force HTTPS
    redirect scheme https if !{ ssl_fc }

    # Bind URL with the right backend
    acl is_airsonic  path_beg -i /airsonic
    use_backend airsonic-backend if is_airsonic

backend airsonic-backend

    # Rewrite all redirects to use HTTPS, similar to what Nginx does in the
    # proxy_redirect directive.
    http-response replace-value Location ^http://(.*)$ https://\1

    # Forward requests to Airsonic server
    server airsonic 127.0.0.1:8080 check
```

You will need to make a couple of changes in the configuration file:
- Be sure to set the right path to your `cert_key.pem` files.
- Change `/airsonic` following your Airsonic context path.
- Change `127.0.0.1:8080` following you Airsonic server location and port.

Restart the HAProxy service:

```
sudo systemctl restart haproxy.service
```
