#Introduction

This document explains how to manage Libresonic logs.

#Main log file

Libresonic ouput log messages into a file called _libresonic.log_ located in the _LIBRESONIC_HOME_ folder.

#How to change log level

One can change the defaults log level by modifying the default application inner configuration.
This application configuration is located in a file called _application.properties_ packaged into the Libresonic.war file. 
Fortunately, there are ways to override the default configuration without having to modify the _application.properties_ inner file.

Those interested in details can have a look here : https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-application-property-files

##If you run Libresonic as a standalone application

Running Libresonic as a standalone application means that you don't deploy Libresonic to a servlet container but run it via a command that looks like this for short :

```
java -jar Libresonic.war
```

In that case you can add your own _application.properties_ file in a _config_ subdirectory to override the default application configuration.

Suppose that you'd like to change the default log level to DEBUG. Follow these steps : 

- create a _config_ folder beside the libresonic.war file
- create a _config/application.properties_ empty file
- add the following line into this file

```
logging.level.root=DEBUG
```

- restart Libresonic

The _config/application.properties_ file can contain any logging configuration directive. 
You can fine tune the log level on any java package by adding a line like : 

```
logging.level.package=LEVEL
```

where package must be replaced with a real java package name, and LEVEL must be replaced with a real level code. 

Allowed levels are : 
- ERROR
- WARN
- INFO
- DEBUG
- TRACE