---
layout: docs
title: Airsonic prerequisites installation
permalink: /docs/install/prerequisites/
---
To get Airsonic running, we are going to install OpenJDK 8 or Oracle JDK 8, set the default `JAVA_HOME`, and finally deploy our Airsonic WAR package.

> **NOTE**: if you are running Airsonic on an ARM platform and you experience extremely long startup times (on the order of 20-30 minutes), you should install Oracle's JDK/JRE. There are known performance issues with OpenJDK under ARM.

#### Install OpenJDK 8

##### On Debian 9

Install openjdk-8-jre package:

```
sudo apt install openjdk-8-jre
```

Set default JAVA_HOME by using the command below and choose the right version (1.8.x):

```
sudo update-alternatives --config java
```

{% include_relative inc.note.tomcat_java_home.md %}

##### On Debian 8

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

Install openjdk-8-jre package from jessie-backports:

```
sudo apt-get install -t jessie-backports openjdk-8-jre
```

Set default JAVA_HOME by using the command below and choose the right version (1.8.x):

```
sudo update-alternatives --config java
```

{% include_relative inc.note.tomcat_java_home.md %}

##### On Ubuntu > 16.04

Install openjdk-8-jre package:

```
sudo apt-get install openjdk-8-jre
```

Set default JAVA_HOME by using the command below and choose the right version (1.8.x):

```
sudo update-alternatives --config java
```

{% include_relative inc.note.tomcat_java_home.md %}

##### On Red Hat / Fedora

Please follow this [well documented guide](https://www.digitalocean.com/community/tutorials/how-to-install-java-on-centos-and-fedora#install-oracle-java-8) to install Java 8  and set default JAVA_HOME on your device.

##### On Windows

Download the JDK 8 .exe package from the [JDK download page](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) and install it.

Then locate the Java installation directory.
> **NOTE**: If you didn't change the path during installation, it'll be something like `C:\Program Files\Java\jdk1.8.0_65`

Do one of the following:
- Windows 7 – Right click My Computer and select Properties > Advanced
- Windows 8 – Go to Control Panel > System > Advanced System Settings

Click the Environment Variables button.

Under System Variables, click New.

In the Variable Name field, enter either:
- JAVA_HOME if you installed the JDK (Java Development Kit)
- JRE_HOME if you installed the JRE (Java Runtime Environment)

In the Variable Value field, enter your JDK or JRE installation path.

If the path contains spaces, use the shortened path name.
For example, `C:\Progra~1\Java\jdk1.8.0_65`.
> **NOTE**: For Windows users on 64-bit systems:
- Progra~1 = `'Program Files'`
- Progra~2 = `'Program Files(x86)'`

Click OK and Apply Changes as prompted

##### On MacOS

Download the _JDK 8 .dmg_ package from the [JDK download page](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) and install it.

Run the following lines in your terminal:

```sh
# check to see if you have 'java -version'. if not, run the command below
# if you run zsh, replace .bash_profile with .zshrc

echo export JAVA_HOME="$(/usr/libexec/java_home -v 1.8)" >> ~/.bash_profile && \
source ~/.bash_profile
```

To easily load Airsonic, and start it with your system services, you should also install the [Homebrew Services](https://github.com/Homebrew/homebrew-services) tap.

```
brew tap homebrew/services
```
