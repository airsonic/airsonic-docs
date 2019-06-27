---
layout: docs
title: Install Airsonic from source
permalink: /docs/install/source/
---
##### Prerequisites

In order to build, install, and run Airsonic, you will need:
- [A JDK installation, 1.8.x series of OpenJDK or Oracle JDK 8+ should work.](/docs/install/prerequisites)
- A recent version of [Maven](http://maven.apache.org/).

#### Test your system

Confirm your Maven installation:

```
which mvn
```

Confirm that the `$JAVA_HOME` environment variable is set:

```
echo $JAVA_HOME
```

If Java is installed, but the `JAVA_HOME` variable not set, be sure to set it before you continue.

#### Download Airsonic

Clone the Airsonic repo:

```
git clone https://github.com/airsonic/airsonic.git
cd airsonic
```

Airsonic version are tagged so you simply need to checkout the version you need:

> `master` being our current develop branch

```
git checkout v{{ site.stable_version }}
```

#### Building

##### Build Airsonic .war package

Using Maven, build Airsonic:

```
mvn clean package
```

You should now have a `.war` file:

```
ls -l airsonic-main/target/airsonic.war
```
```
airsonic-main/target/airsonic.war
```

##### Build Airsonic Docker image

Ensure that you have a working jdk, maven installed, and access to docker via the user you're using:

```
$ javac -version
javac 1.8.0_212
$ which mvn
/usr/bin/mvn
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```


Using maven, build Airsonic with the docker profile:

```
$ mvn clean package -P docker
...
[INFO] Reactor Summary:
[INFO] 
[INFO] Airsonic 10.4.0-SNAPSHOT ........................... SUCCESS [  0.245 s]
[INFO] Subsonic REST API .................................. SUCCESS [  1.652 s]
[INFO] Sonos API .......................................... SUCCESS [  1.745 s]
[INFO] Airsonic Main ...................................... SUCCESS [04:57 min]
[INFO] Airsonic Docker Image 10.4.0-SNAPSHOT .............. SUCCESS [ 11.098 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
...
```

Note the new docker image:

```
$ docker images | grep airsonic
airsonic/airsonic               10.4.0-SNAPSHOT     1f1ca2aaa170        About a minute ago   225 MB
```

