---
layout: docs
title: Installing Libresonic on a Raspberry Pi
permalink: /docs/install/example/raspberrypi/
---

This guide will wallk you through the process of deploying Libresonic on a Raspbery Pi running Rapsbian (Debian Jessie) using tomcat.

#### Install required packages

Install tomcat8 and oracle-java8-jdk:

```
sudo apt install oracle-java8-jdk tomcat8
```

> Note: We suggest not to use the OpenJDK package because Libresonic will take more than 1 hour to deploy. See [this issue](https://github.com/Libresonic/libresonic/issues/281) for more details.

#### Deploy Libresonic

Download the latest `libresonic.war` package from the [download page](/download), or with the command below:

```
wget {{ site.repo }}/libresonic-v{{ site.stable_version }}.war
```

Create the Libresonic directory and assign ownership to the Tomcat system user (if running tomcat as a service):

```
sudo mkdir /var/libresonic/
sudo chown -R tomcat8:tomcat8 /var/libresonic/
```

Stop the tomcat8 service:

```
sudo systemctl stop tomcat8.service
```

Remove the possible existing libresonic files from the TOMCAT_HOME:

```
sudo rm /var/lib/tomcat8/webapps/libresonic.war
sudo rm -R /var/lib/tomcat8/webapps/libresonic/
sudo rm -R /var/lib/tomcat8/work/*
```

Move the downloaded WAR file in the TOMCAT_HOME/webapps/ folder:

```
sudo mv libresonic-v{{ site.stable_version }}.war /var/lib/tomcat8/webapps/libresonic.war
```

Restart the tomcat8 service:

```
sudo systemctl start tomcat8.service
```

> Be patient (several minutes at least on Raspberry pi 3), you can follow the deployment using `sudo tail -f /var/log/tomcat8/catalina.out` and wait for the following message:
```
INFO: Deployment of web application archive /var/lib/tomcat8/webapps/libresonic.war has finished in 146,192 ms
```

Libresonic should be running at [http://localhost:8080/libresonic](http://localhost:8080/libresonic) if installed locally, replace `localhost` with your server IP address if installed remotly.

#### Set up a reverse proxy

If you have a nginx reverse proxy, add to `/etc/nginx/sites-available/default` (or another config file if you're not using the default one) and add just before the last `}` :

```
location /libresonic/ {

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

[https://yourdomain.com/libresonic](https://yourdomain.com/libresonic)

#### Set up a transcoder

Open your `/etc/apt/source.list`:

```
sudo nano /etc/apt/source.list
```

Add the backports repo to it:

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

Create a `transcode` directory within your `LIBRESONIC_HOME` directory:

```
mkdir /var/libresonic/transcode
```

Within the `transcode` directory symlink to ffmpeg and verify correct permissions
```
cd transcode/
ln -s /usr/bin/ffmpeg
ls -alh
```
```
lrwxrwxrwx 1 user user   15 mai    4 19:57 ffmpeg -> /usr/bin/ffmpeg
```

> Note that `user` has to be the user that runs Libresonic
