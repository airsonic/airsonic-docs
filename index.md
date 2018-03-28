---
layout: docs
title: Documentation
permalink: /docs/
---
Welcome to the Airsonic documentation. This guide describes installation process for Airsonic, a free, web-based media streamer, providing ubiquitous access to your music.

> NOTE: **Before following any docs, be sure that your system is up-to-date!**

First of all you need to install a working JDK. Follow this [guide](/docs/install/prerequisites/) to install it.

Here you can pick your installation docs for a:

- [WAR package (Tomcat)](/docs/install/war-tomcat)
- [WAR package (Standalone)](/docs/install/war-standalone)

- [Homebrew](/docs/install/homebrew)
- [Docker container](/docs/install/docker)

- [Build from source](/docs/install/source)

If you come from [Subsonic](http://www.subsonic.org/pages/index.jsp), you can migrate using our [migration docs](/docs/migrate).

> NOTE: Since we forked Subsonic version 5.3, you might need to do some extra cleanup if migrating from a higher version.

After installing Airsonic, you may want to put Airsonic behind a reverse proxy to access Airsonic on the HTTP ports or/and enable SSL. Use a [guide](/docs/proxy) in the list below:
- [Configure Apache proxy](/docs/proxy/apache)
- [Configure Nginx proxy](/docs/proxy/nginx)
- [Configure Haproxy proxy](/docs/proxy/haproxy)

Transcoders are used by Airsonic to convert media from their on disk format to a format that can be consumed by clients. Use our docs to set up the [transcode binaries](/docs/transcode).

Using an external database for large music collection can only be useful, just follow our [database docs](/docs/database) to set it up.
