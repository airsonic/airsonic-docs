---
layout: docs
title: Manage Airsonic logs
permalink: /docs/logging/
---
This guide explains how to manage Airsonic logs.

### Main log file

Airsonic ouput log messages into a file called `airsonic.log` located in the `AIRSONIC_HOME` folder.

#### Change log level using Tomcat

One can change the defaults log level by modifying the default application inner configuration.

This application configuration is located in a file called `application.properties` packaged into the Airsonic.war file. Fortunately, there are ways to override the default configuration without having to modify the `application.properties` inner file.

Those interested in details can have a look at [this spring.io document](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-application-property-files).

#### Change log level running standalone

Running Airsonic as a standalone application means that you don't deploy Airsonic to a servlet container but run it via a command that looks like this for short :

> **NOTE**: See [stand-alone installation doc](/docs/install/war-standalone/) for more details

```
java -jar airsonic.war
```

In that case you can add your own `application.properties` file in a `config` subdirectory to override the default application configuration.

Suppose that you'd like to change the default log level to DEBUG. Follow these steps:

- create a `config` folder beside the `airsonic.war` file
- create a `config/application.properties` empty file
- add the following line into this file

```
logging.level.root=DEBUG
```

- restart Airsonic

The `config/application.properties` file can contain any logging configuration directive.
You can fine tune the log level on any java package by adding a line like:

```
logging.level.package=LEVEL
```

where package must be replaced with a real java package name, and LEVEL must be replaced with a real level code.

Allowed levels are:
- `ERROR`
- `WARN`
- `INFO`
- `DEBUG`
- `TRACE`

Interesting packages to watch for are:

```
# Set Airsonic-specific loggers to 'DEBUG'
logging.level.org.airsonic=DEBUG

# Set all loggers to 'DEBUG' (warning: generates a lot of logs)
logging.level.root=DEBUG

# Set up SQL logging (warning: may leak passwords/keys/personal data)
logging.level.org.springframework.jdbc.core.JdbcTemplate=DEBUG
logging.level.org.springframework.jdbc.core.StatementCreatorUtils=TRACE
```
