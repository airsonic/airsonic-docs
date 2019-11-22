---
layout: docs
title: Airsonic Applications
permalink: /docs/update/
---

{% include_relative install/inc.download.md %}

#### Standalone deployment

If you're using systemd, a simple `systemctl airsonic restart` should be
enough.

#### Tomcat deployment

```
service tomcat8 stop
cd /usr/local/apache-tomcat-8.0/webapps
rm -R ROOT
mv airsonic.war ROOT.war
chown www:www ROOT.war
service tomcat8 start
```
