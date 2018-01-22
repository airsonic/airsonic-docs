---
layout: docs
title: Homebrew installation
permalink: /docs/install/homebrew/
---
This page describes how to run install Airsonic with Homebrew on macOS [Airsonic Homeprew tap](https://github.com/airsonic/homebrew-airsonic/).

This installation method is designed for easy install and upgrades, and uses sane defaults. It might not work as well for very particular / special configurations.

##### Prerequisites

In order to install and Airsonic on macOS all you need is [Homebrew](https://brew.sh).

Airsonic requires a recent Java runtime. The formula requires this cask, but it will not be installed automatically.

```
brew cask install java
```

All other dependencies will be installed and linked automatically.

To easily load Airsonic, and start it with your system services, you should also install the [Homebrew Services](https://github.com/Homebrew/homebrew-services) tap.

```
brew tap homebrew/services
```

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

##### Running Airsonic with Homebrew Services (preferred)

```
brew services start airsonic
```

##### Running Airsonic with launchd so that when you login Airsonic is launched

```
cp `(brew --prefix)`/Cellar/airsonic/VERSION/homebrew.mxcl.airsonic.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.airsonic.plist
```

> Replace VERSION with your Airsonic version
```

##### Running Airsonic with launchd at system boot needing no user login (background/headless)

```
cp `(brew --prefix)`/Cellar/airsonic/VERSION/homebrew.mxcl.airsonic.plist /Library/LaunchDaemons/
```

> Replace VERSION with your Airsonic version (and chown the .plist file to be root:wheel)
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
