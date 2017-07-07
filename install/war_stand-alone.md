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
wget {{ site.repo }}/airsonic-v{{ site.stable_version }}.war
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
