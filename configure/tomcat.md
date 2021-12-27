---
layout: docs
title: Configure Tomcat running Airsonic
permalink: /docs/configure/tomcat/
---
### How to add Java parameters

As described in the [RUNNING.txt](http://tomcat.apache.org/tomcat-8.0-doc/RUNNING.txt) doc provided by tomcat, you can create a file named `setenv.sh` or for windows `setenv.bat` in the Tomcat home `bin` folder to  modify the java args.

Simply add the parameter like this `-D${PARAMETER}=${ARGUMENT}`.

Here is an example of a `setenv.sh` file (`setenv.bat` has slightly different syntax):

```sh
export JAVA_OPTS="$JAVA_OPTS -Dairsonic.home=/var/airsonic"
```

#### Airsonic home

Parameter = `airsonic.home`

Exemple = `-Dairsonic.home=/var/airsonic`

Dictates the folder where Airsonic will store its logs, settings, transcode binaries, index and database if using the default H2 database. As such it is recommended to backup this folder.

> Default: `/var/airsonic`

#### Java memory allocation

Example = `-Xmx512m`

Here we will allocate 512 Mb of memory to Airsonic's Java process.

### How to configure Tomcat parameters

When running Airsonic with tomcat some parameters cannot be set using Java args but need to be direclty changed in tomcat.

#### Tomcat port

First you need to locate `server.xml` which by default should be in the ``${TOMCAT_HOME}/conf/`` folder (e.g. for Debian it will be `/var/lib/tomcat8/conf/server.xml`).

Then find the following similar statement:

```xml
<!-- Define a non-SSL HTTP/1.1 Connector on port 8180 -->
   <Connector port="8080" maxHttpHeaderSize="8192"
              maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
              enableLookups="false" redirectPort="8443" acceptCount="100"
              connectionTimeout="20000" disableUploadTimeout="true" />

```
Or
```xml
<!-- A "Connector" represents an endpoint by which requests are received
     and responses are returned. Documentation at :
     Java HTTP Connector: /docs/config/http.html (blocking & non-blocking)
     Java AJP  Connector: /docs/config/ajp.html
     APR (HTTP/AJP) Connector: /docs/apr.html
     Define a non-SSL HTTP/1.1 Connector on port 8080
-->
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

Tomcat’s `server.xml` file cites it’s runs on port 8080. Change the `Connector port="8080"` port to any other port number.

Finally save the `server.xml` file and restart tomcat8.

#### Context path

By default, Tomcat will deploy any ``.war`` file in ``webapps` directory
with the context name matching the filename. For example, ``airsonic.war``
will be deployed to the ``/airsonic`` context.

If you don't want this to happen, you need to locate your ``airsonic.war``
file somewhere other than the tomcat ``webapps`` directory and apply some
configuration.

Locate `server.xml` which by default should be in the ``${TOMCAT_HOME}/conf/`` folder (e.g. for Debian it will be `/var/lib/tomcat8/conf/server.xml`).

You will need to add the following right above the `</Host>` tag:

```xml
<Context path="" docBase="airsonic" debug="0" reloadable="true">
  <WatchedResource>WEB-INF/web.xml</WatchedResource>
</Context>
<Context path="ROOT" docBase="ROOT"> <!-- Default set of monitored resources -->
  <WatchedResource>WEB-INF/web.xml</WatchedResource>
</Context>
```

To clarify, if you want to serve airsonic from the ``/air` context and you have the
airsonic.war file located in ``/opt/airsonic/airsonic.war``, make sure the
configuration looks something like this:

```xml
<Context path="/air" docBase="/opt/airsonic/airsonic.war" debug="0" reloadable="true">
  <WatchedResource>WEB-INF/web.xml</WatchedResource>
</Context>
```

Finally save the `server.xml` file and restart tomcat8.

#### hostname configuration

If you find your reverse proxy isn't working properly and is returning 404 results
you don't expect, you might need to set the hostname configuration in the Tomcat
``server.xml`` configuration file.

For example, if your instance is running on a host named ``mymusic.example.com``,
the config line should look like:

```
<Host name="mymusic.example.com"  appBase="webapps"
unpackWARs="true" autoDeploy="true">
```
