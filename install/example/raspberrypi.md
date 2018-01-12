---
layout: docs
title: Installing Airsonic on a Raspberry Pi
permalink: /docs/install/example/raspberrypi/
---
This guide will wallk you through the process of deploying Airsonic on a Raspbery Pi running Rapsbian (Debian Jessie) using tomcat.

#### Install required packages

Install tomcat8 and oracle-java8-jdk:

```
sudo apt-get install oracle-java8-jdk tomcat8
```

> Note: We suggest not to use the OpenJDK package because Airsonic will take more than 1 hour to deploy. See [this issue](https://github.com/airsonic/airsonic/issues/283) for more details.

If you could not set JAVA_HOME using `sudo update-alternatives --config java`, do the following:

List the available Java versions:

```
ls -l /usr/lib/jvm
```
```
drwxr-xr-x 8 root root 4096 Jan  6 22:24 java-8-oracle
```

Open `/etc/default/tomcat8` and hardcode the path to JAVA_HOME:

```
# The home directory of the Java development kit (JDK). You need at least
# JDK version 7. If JAVA_HOME is not set, some common directories for
# OpenJDK and the Oracle JDK are tried.
JAVA_HOME=/usr/lib/jvm/java-8-oracle/
```

#### Deploy Airsonic

Download the latest `airsonic.war` package from the [download page](/download), or with the command below:

```
wget {{ site.repo }}/download/v{{ site.stable_version }}/airsonic.war
```

Create the Airsonic directory and assign ownership to the Tomcat system user (if running tomcat as a service):

```
sudo mkdir /var/airsonic/
sudo chown -R tomcat8:tomcat8 /var/airsonic/
```

Stop the tomcat8 service:

```
sudo systemctl stop tomcat8.service
```

Remove the possible existing airsonic files from the TOMCAT_HOME:

```
sudo rm /var/lib/tomcat8/webapps/airsonic.war
sudo rm -R /var/lib/tomcat8/webapps/airsonic/
sudo rm -R /var/lib/tomcat8/work/*
```

Move the downloaded WAR file in the TOMCAT_HOME/webapps/ folder:

```
sudo mv airsonic.war /var/lib/tomcat8/webapps/airsonic.war
```

Restart the tomcat8 service:

```
sudo systemctl start tomcat8.service
```

> Be patient (several minutes at least on Raspberry pi 3), you can follow the deployment using `sudo tail -f /var/log/tomcat8/catalina.out` and wait for the following message:
```
INFO: Deployment of web application archive /var/lib/tomcat8/webapps/airsonic.war has finished in 146,192 ms
```

Airsonic should be running at [http://localhost:8080/airsonic](http://localhost:8080/airsonic) if installed locally, replace `localhost` with your server IP address if installed remotely.

#### Set up a reverse proxy

If you have a nginx reverse proxy, add to `/etc/nginx/sites-available/default` (or another config file if you're not using the default one) and add just before the last `}` :

```
location /airsonic/ {

proxy_pass http://localhost:8080;
proxy_set_header X-Forwarded-Host $host;
proxy_set_header X-Forwarded-Server $host;
proxy_set_header X-Real-IP         $remote_addr;
proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto https;
proxy_set_header Host              $http_host;
proxy_max_temp_file_size           0;
proxy_redirect                     http:// https://;
}
```

Restart nginx:

```
sudo service nginx restart
```

And then go to:

[https://yourdomain.com/airsonic](https://yourdomain.com/airsonic)

#### Set up a transcoder

Edit your `/etc/apt/sources.list`:

```
sudo nano /etc/apt/sources.list
```

Add the backports repository to it:

```
deb http://ftp.fr.debian.org/debian/ jessie-backports main contrib
```

Update your package list:

```
sudo apt-get update
```

Install ffmpeg package from jessie-backports:

```
sudo apt-get install ffmpeg -t jessie-backports
```

Create a `transcode` directory within your `AIRSONIC_HOME` directory:

```
sudo mkdir /var/airsonic/transcode
```

Within the `transcode` directory symlink to ffmpeg and verify correct permissions
```
cd /var/airsonic/transcode/
sudo ln -s /usr/bin/ffmpeg
sudo chown -R tomcat8:tomcat8 /var/airsonic
ls -l
```
```
lrwxrwxrwx 1 tomcat8 tomcat8 15 Jan  7 09:46 ffmpeg -> /usr/bin/ffmpeg
```

> Note that `user` has to be the user that runs Airsonic
