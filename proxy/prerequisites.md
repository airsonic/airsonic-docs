---
layout: docs
title: Setting up a reverse proxy
permalink: /docs/proxy/prerequisites/
---
A reverse proxy is a public-facing web server sitting in front of an internal server such as Airsonic. The Airsonic server never communicates with the outside ; instead, the reverse proxy handles all HTTP(S) requests and forwards them to Airsonic.

This is useful in many ways, such as gathering all web configuration in the same place. It also handles some options (HTTPS) much better than the bundled Airsonic server or a servlet container such as Tomcat.

This guide assumes you already have a working Airsonic installation after following the [installation guide](/docs/install).

### Getting a TLS certificate

This guide assumes you already have a TLS certificate. [Let's Encrypt](https://letsencrypt.org/getting-started/) currently provides such certificates for free using the [certbot software](https://certbot.eff.org/).

### Configure Airsonic

A few settings should be tweaked via Spring Boot or Tomcat
configuration:

  - Set the context path to `/airsonic`
  - Set the correct address to listen to
  - Set the correct port to listen to

### Reverse proxy configuration

##### How it works

Airsonic expects proxies to provide information about their incoming URL so that Airsonic can craft it when needed.
To do so, Airsonic looks for the following HTTP headers:

 - `X-Forwarded-Host`
   - Provides server name and optionally port in the case that the proxy is on a non-standard port
 - `X-Forwarded-Proto`
   - Tells Airsonic whether to craft an HTTP or HTTPS url
 - `X-Forwarded-Server`
   - This is only a fallback in the case that `X-Forwarded-Host` is not available

Currently this is used wherever, `NetworkService#getBaseUrl` is called. A couple notable places include:

- Stream urls
- Share urls
- Coverart urls

### Provided configurations

Use a guide in the list below:
- [Configure Apache proxy](/docs/proxy/apache)
- [Configure Nginx proxy](/docs/proxy/nginx)
- [Configure Haproxy proxy](/docs/proxy/haproxy)
