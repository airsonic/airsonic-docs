---
layout: docs
title: Install Airsonic from source
permalink: /docs/install/source/
---
##### Prerequisites

In order to build, install, and run Airsonic, you will need:
- [A JDK installation, 1.8.x series of OpenJDK or Oracle JDK 8+ should work.](/docs/install/prerequisites)
- A recent version of [Maven](http://maven.apache.org/).
- Optional: lintian and fakeroot, for .deb package.
- Optional: rpm and rpmlint, for .rpm package.

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

> If you want to build the development version, change the branch to `develop`
```
git checkout develop
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
