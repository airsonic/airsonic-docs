---
layout: docs
title: Stand-alone WAR installation
permalink: /docs/install/war-standalone/
---
If you'd prefer not to use a Tomcat container, you can also run Airsonic as a standalone application.
Note that, in that case, airsonic will available at `http://localhost:8080` (and not `http://localhost:8080/airsonic`).

##### Prerequisites

In order to install and run Airsonic, you will need:
- [A JDK installation, 1.8.x series of OpenJDK or Oracle JDK 8+ should work.](/docs/install/prerequisites)

#### Run Airsonic WAR package

Download the latest Airsonic .war package from the [download page](/download), or with the command below:

```
wget {{ site.repo }}/download/v{{ site.stable_version }}/airsonic.war
```

Create the Airsonic directory and assign ownership to the user that will run Airsonic:

```
sudo mkdir /var/airsonic/
sudo chown -R $USER:$GROUP /var/airsonic/
```

Now you can simply run java against the airsonic.war package:

```
java -jar airsonic.war
```

Airsonic should be running at [http://localhost:8080](http://localhost:8080) if installed locally, replace `localhost` with your server IP address if installed remotely.


##### Systemd
To go a bit further than just running the airsonic manually, one can setup
integration with Systemd.  Systemd is an init system for most linux systems. It
allows one to run airsonic on boot. These steps below were done on a Fedora host,
however the steps should be similar for other Linux distributions.

Setup dedicated airsonic user:

```
useradd airsonic
```

Setup Airsonic data dir:

```
mkdir /var/airsonic
chown airsonic /var/airsonic
```

Download the war:

```
wget {{ site.repo }}/download/v{{ site.stable_version }}/airsonic.war  --output-document=/var/airsonic/airsonic.war
```

Setup systemd service:

(Under Debian : replace `/etc/sysconfig` in the command line below with `/etc/default`)

```
wget https://raw.githubusercontent.com/airsonic/airsonic/master/contrib/airsonic.service -O /etc/systemd/system/airsonic.service
systemctl daemon-reload
systemctl start airsonic.service
systemctl enable airsonic.service
wget https://raw.githubusercontent.com/airsonic/airsonic/master/contrib/airsonic-systemd-env -O /etc/sysconfig/airsonic
```

Review/Modify any startup settings in /etc/sysconfig/airsonic.
