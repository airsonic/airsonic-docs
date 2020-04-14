---
layout: docs
title: Using Maven
permalink: /docs/developer/maven/
---

Maven is the build tool used to build Airsonic. It is a command-line tool that
encapsulates all the parts of the build, from compiling the source code to
packaging, and manages our dependencies.

The following page explains how Maven is structured for Airsonic, including
many useful commands. Java IDE such as IntelliJ can run a subset of Maven
features, though some are only available on the command-line.

# Building Airsonic

The following command can be used to build the Airsonic WAR package (`airsonic.war`):

```bash
$ mvn package
```

The following command can be used to run a full Airsonic build, including
Docker image and integration tests (this is what our CI currently runs):

```bash
$ mvn -P integration-test verify
```

The following command can be used to clean the build outputs:

```bash
$ mvn clean
```

Commands can also be combined, such as:

```bash
$ mvn -P integration-test clean verify
```

# Configuration

Maven's main configuration is split into multiple `pom.xml` files, starting
from the one at the root of the repository.

# Features

## WAR packaging

The main Airsonic package is an executable Spring Boot WAR file
(`airsonic.war`), that can be both run in standalone mode or deployed on a
servlet container (such as Tomcat).

This is provided by `spring-boot-maven-plugin` in `airsonic-main/pom.xml`.

## Checkstyle

The Maven build runs a "style checker", which checks the Airsonic code for
common issues or style inconsistencies.

This is provided by `maven-checkstyle-plugin`, defined in `pom.xml`.

## Dependency checker

The Maven build runs the Dependency Checker plugin, which checks the Airsonic
code for security issues (CVE) or common dependency resolution errors (missing
packages, unused dependencies).

Disabling the dependency checker:

```bash
$ mvn package -Ddependency-check.skip=true
```

This is provided by `dependency-check-maven`, defined in `pom.xml`.

## Unit tests (Maven Surefire)

Airsonic unit tests are run using the Maven Surefire plugin.

Running all tests:

```bash
$ mvn test
```

Running a specific test or class of tests:

```bash
$ mvn -Dtest='*Service*Test' -DfailIfNoTests=false test
```

Package while disabling all tests:

```bash
$ mvn package -DskipTests
```

This plugin binds to the `test` phase of the lifecycle, which you'll notice is
before the `package` phase. That means at that point there isn't even a war/jar
or any such packaging. So your unit tests run directly on the compiled classes
in their loose individual forms (`*.class` files usually sitting in
`target/generated` or `target/classes` and `target/test-classes`).

## Integration tests (Maven Failsafe)

Airsonic unit tests are run using the Maven Failsafe plugin in a Docker container.

Running all tests:

```bash
$ mvn -Pintegration-test verify
```

Running a specific test or class of tests:

```bash
$ mvn -Dit.test='StreamIT' -DfailIfNoTests=false test
```

Package while disabling all tests:

```bash
$ mvn -Pintegration-test verify -DskipTests
```

This plugin binds to the `integration-test` phase of the Maven lifecycle. This
happens after the packaging is done, so now (contrary to Surefire) your tests
actually run against the full packaged version of your code (that is,
`airsonic.war`) instead of individual class files.

There is a dedicated setup and teardown phase, that are run even if the
`integration-test` phase fails. The dedicated phases will run regardless and
the build only fails afterwards. This isn't true, for instance, say the `test`
phase; if your test phase fails, maven will err out and not even go to the next
(`package`) phase.

Finally it is important to note that just running the integration-test phase is
useless. You need to run verify because running the tests by itself doesn't do
much. You need to verify whether the tests passed or not.

## Other commands

List all dependencies (useful for checking conflicts on upgrades):

```bash
$ mvn dependency:tree
```

Get help on a plugin:

```bash
$ mvn help:describe -Dplugin=org.owasp:dependency-check-maven
```
