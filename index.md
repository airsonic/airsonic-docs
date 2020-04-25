---
layout: docs
title: Documentation
permalink: /docs/
---
Welcome to the Airsonic documentation. This guide describes installation process for Airsonic, a free, web-based media streamer, providing ubiquitous access to your music.

**Before following any docs, be sure that your system is up-to-date!**

Before installing Airsonic, you will have to install a working JDK. Follow this [guide](/docs/install/prerequisites/) to install it.

Here you can pick your installation docs for:

- [WAR package (Tomcat)](/docs/install/war-tomcat)
- [WAR package (Standalone)](/docs/install/war-standalone)
- [Docker container](/docs/install/docker)
- [Build from source](/docs/install/source)
- [Homebrew](/docs/install/homebrew)

If you don't know which one you should pick, odds are that the
[standalone version](/docs/install/war-standalone) is the right choice for you.

If you come from [Subsonic](http://www.subsonic.org/pages/index.jsp), you can migrate using our [migration docs](/docs/migrate).

After installing, you may want to put Airsonic behind a reverse proxy to access Airsonic on the HTTP(S) ports or enable SSL. Use a [proxy docs](/docs/proxy/prerequisites/) in the list below:
- [Configure Apache proxy](/docs/proxy/apache)
- [Configure Nginx proxy](/docs/proxy/nginx)
- [Configure Haproxy proxy](/docs/proxy/haproxy)
- [Configure Caddy proxy](/docs/proxy/caddy)

Using an external database for large music collection can only be useful, just follow our [database docs](/docs/database) to set it up.

Transcoders are used by Airsonic to convert media from their on disk format to a format that can be consumed by clients. Use our docs to set up the [transcode binaries](/docs/transcode).

Don't forget to keep your airsonic instance [up to date](/docs/update)!
