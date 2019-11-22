---
layout: docs
title: Homebrew installation
permalink: /docs/install/homebrew/
---
This page describes how to run install Airsonic with Homebrew on macOS [Airsonic Homeprew tap](https://github.com/airsonic/homebrew-airsonic/).

This installation method is designed for easy install and upgrades, and uses sane defaults. It might not work as well for very particular / special configurations.

##### Prerequisites

In order to install and run Airsonic, you will need:
- [A JDK installation, 1.8.x series of OpenJDK or Oracle JDK 8+ should work.](/docs/install/prerequisites)

##### Installing

The Airsonic formula currently resides in a custom project tap. It will be submitted to the official Homebrew formula repo at a future date.

```
brew tap airsonic/airsonic
brew install airsonic
```

##### Upgrading

```
brew upgrade airsonic
```

##### Using Homebrew Services (preferred)

```
brew services start airsonic
```

##### Using launchd

```
cp `(brew --prefix)`/Cellar/airsonic/{{ site.stable_version }}/homebrew.mxcl.airsonic.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.airsonic.plist
```

##### Using launchd (headless)

```
cp `(brew --prefix)`/Cellar/airsonic/{{ site.stable_version }}/homebrew.mxcl.airsonic.plist /Library/LaunchDaemons/
```

##### Runtime settings used

Here is a brief list of the runtime flags used, for reference.

```
Xmx512m
Dlogging.file=#{var}/log/airsonic.log
Dlogging.level.root=ERROR
Dserver.host=0.0.0.0
Dserver.port=4040
Dserver.context-path=/
Dairsonic.home=#{workingdir}
Djava.awt.headless=true
```

##### Updating Runtime settings

Use your favorite text editor and open `$(brew --prefix)/Cellar/airsonic/{{ site.stable_version }}/homebrew.mxcl.airsonic.plist`

Edit any of the following properties:

```xml
<string>-Xmx512m</string>  <!-- Max Memory -->
<string>-Dlogging.file=/usr/local/var/log/airsonic.log</string>
<string>-Dlogging.level.root=ERROR</string>
<string>-Dserver.host=0.0.0.0</string>
<string>-Dserver.port=4040</string>
<string>-Dserver.context-path=/</string>  <!-- localhost:port/context-path, e.g.: /airsonic -->
<string>-Dairsonic.home=/usr/local/var/airsonic</string>
<string>-Djava.awt.headless=true</string>
<string>-jar</string>
<string>/usr/local/Cellar/airsonic/{{ site.stable_version }}/airsonic.war</string>
```
