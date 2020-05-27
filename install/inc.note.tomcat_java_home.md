> **NOTE**: if Tomcat8 didn't get the right JAVA_HOME you can set it in `/etc/default/tomcat8`:
1. List the available Java version:
```
ls -l /usr/bin/jvm/
```
```
default-java -> java-1.8.0-openjdk-amd64
java-1.7.0-openjdk-amd64 -> java-7-openjdk-amd64
java-1.8.0-openjdk-amd64 -> java-8-openjdk-amd64
java-7-openjdk-amd64
java-8-openjdk-amd64
```
2. Open `/etc/default/tomcat8` and paste the right version to these lines:
```
# The home directory of the Java development kit (JDK). You need at least
# JDK version 7. If JAVA_HOME is not set, some common directories for
# OpenJDK and the Oracle JDK are tried.
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
