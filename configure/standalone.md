---
layout: docs
title: Configure Airsonic running standalone
permalink: /docs/configure/standalone/
---
### How to add Java parameters

When running the standalone package, add `-D${PARAMETER}=${ARGUMENT}` to the `java` command line right before the
`-jar` argument.

Here is an example for linux (windows users will want to use their OS specific path syntax i.e. `C:\\your\path`):

```java
java -Dairsonic.home=/var/airsonic -jar airsonic.war
```

### Common parameters

#### Server port

Parameter = `server.port`

Example = `-Dserver.port=8080`

Changes the port that the standalone package listens on.

> Default: 8080

> **NOTE**: This property only applies for spring boot/standalone config.

#### Server address

Parameter = `server.address`

Example = `-Dserver.address=127.0.0.1`

Changes the address that the standalone package listens on.

> Default: not set and listens to all addresses

> **NOTE**: This property only applies for spring boot/standalone config.

#### Context path

Parameter = `server.context-path`

Example = `-Dserver.context-path=/audio`

> **NOTE**: Please do not add the last `/` in the context path

Changes the URL path that the standalone package listens on.

> Default: not set and listens at `/`

> **NOTE**: This property only applies for spring boot/standalone config.

#### Airsonic home

Parameter = `airsonic.home`

Exemple = `-Dairsonic.home=/var/airsonic`

Dictates the folder where Airsonic will store its logs, settings, transcode binaries, index and database if using the default H2 database. As such it is recommended to backup this folder.

> Default: `/var/airsonic`

#### Java memory allocation

Example = `-Xmx512m`

Here we will allocate 512 MB of memory to Airsonic's Java process.

> **NOTE**: Airsonic has been observed to run quite well for a single user with only 200 MB and an Intel Atom C2350.

### Advanced parameters

A full list of parameters can be found [here](https://docs.spring.io/spring-boot/docs/1.4.5.RELEASE/reference/htmlsingle/#common-application-properties).

Note that not all configurations apply to airsonic, but the important section is the **# EMBEDDED SERVER CONFIGURATION** section.
