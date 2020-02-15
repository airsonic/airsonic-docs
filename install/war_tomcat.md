---
layout: docs
title: Tomcat WAR installation
permalink: /docs/install/war-tomcat/
---
##### Prerequisites

In order to install and run Airsonic with Tomcat, you will need:
- [A JDK installation, 1.8.x series of OpenJDK or Oracle JDK 8+ should work.](docs/install/prerequisites)
- A running [Tomcat](http://tomcat.apache.org/) version 8.5+ server. If you're unfamiliar with Tomcat, there are many [guides](https://www.digitalocean.com/community/tags/java?q=How+to+install+tomcat8&type=tutorials) on it.

{% include_relative inc.download.md %}

#### Deploy Airsonic WAR package

##### On Debian 8 / Ubuntu > 16.04

Create the Airsonic directory and assign ownership to the Tomcat system user (if running tomcat as a service):

```
sudo mkdir /var/airsonic/
sudo chown -R tomcat8:tomcat8 /var/airsonic/
```

Stop the `tomcat8` service:

```
sudo systemctl stop tomcat8.service
```

Remove the possible existing airsonic files from the `$TOMCAT_HOME`:

```
sudo rm /var/lib/tomcat8/webapps/airsonic.war
sudo rm -R /var/lib/tomcat8/webapps/airsonic/
sudo rm -R /var/lib/tomcat8/work/*
```

Move the downloaded WAR file in the `$TOMCAT_HOME/webapps` folder and assign ownership to the Tomcat system user:

```
sudo mv airsonic.war /var/lib/tomcat8/webapps/airsonic.war
sudo chown tomcat8:tomcat8 /var/lib/tomcat8/webapps/airsonic.war
```

Restart the `tomcat8` service:

```
sudo systemctl start tomcat8.service
```

> **NOTE**: it may take ~30 seconds after the service restarts for Tomcat to fully deploy the app. You can monitor /var/log/tomcat8/catalina.out for the following message:
```
INFO: Deployment of web application archive /var/lib/tomcat8/webapps/airsonic.war has finished in 46,192 ms
```

Airsonic should be running at [http://localhost:8080/airsonic](http://localhost:8080/airsonic) if installed locally, replace `localhost` with your server IP address if installed remotely.

##### On Debian 9 / Ubuntu > 18.04

Debian 9 and Ubuntu 18.04 propose the `tomcat9` package.

Create the Airsonic directory and assign ownership to the Tomcat system user (if running tomcat as a service):

```
sudo mkdir /var/airsonic/
sudo chown -R tomcat9:tomcat9 /var/airsonic/
```

Stop the `tomcat9` service:

```
sudo systemctl stop tomcat9.service
```

Remove the possible existing airsonic files from the `$TOMCAT_HOME`:

```
sudo rm /var/lib/tomcat9/webapps/airsonic.war
sudo rm -R /var/lib/tomcat9/webapps/airsonic/
sudo rm -R /var/lib/tomcat9/work/*
```

Move the downloaded WAR file in the `$TOMCAT_HOME/webapps` folder and assign ownership to the Tomcat system user:

```
sudo mv airsonic.war /var/lib/tomcat9/webapps/airsonic.war
sudo chown tomcat9:tomcat9 /var/lib/tomcat9/webapps/airsonic.war
```

Make sure that the Tomcat service has access to your music folders, by adding
the following lines to `/etc/systemd/system/tomcat9.service.d/airsonic.conf`
(add as many `ReadWritePaths` lines as necessary):

```
[Service]
ReadWritePaths=/var/airsonic/
```

Reload the systemd configuration:

```
sudo systemctl daemon-reload
```

Restart the `tomcat9` service:

```
sudo systemctl start tomcat9.service
```

> **NOTE**: it may take ~30 seconds after the service restarts for Tomcat to fully deploy the app. You can monitor /var/log/tomcat9/catalina.out for the following message:
```
INFO: Deployment of web application archive /var/lib/tomcat9/webapps/airsonic.war has finished in 46,192 ms
```

Airsonic should be running at [http://localhost:8080/airsonic](http://localhost:8080/airsonic) if installed locally, replace `localhost` with your server IP address if installed remotely.

##### On FreeBSD

Install Tomcat 8.5 by building from source or installing from the package repository:
```
pkg install tomcat85
```
Once installed, have tomcat start on boot by adding a line to `/etc/rc.conf`:
```
# sysrc tomcat85_enable=YES
tomcat_enable: NO -> YES
```
We also want to support unicode (non-english) characters in our file names,
so we will tell Tomcat to use UTF-8. We will also let Tomcat know what timezone
we are in.

Replace `en_US` and `Europe/Paris` with your desired language & timezone. You may
omit the TZ variable to use sysem time:
```
# sysrc tomcat85_env="LANG=en_US.UTF-8 TZ=Europe/Paris"
tomcat85_env:  -> LANG=en_US.UTF-8 TZ=Europe/Paris
```

For more information, follow the complete [installation guide for FreeBSD](docs/install/example/freebsd-freenas/).

##### On Red Hat / Fedora

**Work in progress**

##### On Windows

**Work in progress**

##### On MacOS

**Work in progress**
