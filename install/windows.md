---
layout: docs
title: Windows Installation
permalink: /docs/install/windows/
---
This page describes how to install Airsonic under Windows as Stand-alone WAR, without using Tomcat. It also contains information about how to setup Airsonic as a Windows Service and let it run under an user/service account.

> **NOTE**: If you would like to run Airsonic with HTTPS, take a look at the proxy section about [IIS](/docs/proxy/iis/).

##### Prerequisites

Download the latest [Airsonic Release](https://github.com/airsonic/airsonic/releases), you need the `airsonic.war`.

Install [OpenJDK](https://openjdk.java.net) or another Java implementation. Make sure to add Java to your `PATH` variable.

If you like to run Airsonic as a Windows Service, download the [Non-Sucking Service Manager (NSSM)](https://nssm.cc/download).

##### Install Airsonic

Before we start, doublecheck if Java is working as expected and the `PATH` environment variable is setup correctly. Open a command prompt or the PowerShell console and type `java -version`. This should print the installed Java version.

Create a new directory `C:\Program Files\Airsonic` and move the `airsonic.war` file to this folder.

Create an `airsonic.cmd` file in this folder and add the following content.

```
java.exe -Dairsonic.home="C:\\Program Files\\Airsonic" -Dserver.port=4040 -jar airsonic.war
```

Now you're ready! Start the `airsonic.cmd` file by double clicking on it, wait some seconds and browse to `http://localhost:4040`. You should see the Airsonic logon page.

##### Installing Airsonic as Windows Service

As it might be a bit inconvenient to start Airsonic after every restart of your computer/server by yourself, you can setup a Windows Service. This Windows Service will start Airsonic automatically when you boot your computer, you don't even need to logon.

> **NOTE**: To run a Java application as a Windows Service there would be a [Java Service Wrapper](https://wrapper.tanukisoftware.com), but it's not really easy to setup. That's why we go for NSSM.

Create a new directory `C:\Program Files\NSSM` and copy `nssm.exe` to this folder.

Open a command prompt or the PowerShell console and change the directory to the NSSM folder `cd C:\Program Files\NSSM`.

Type `nssm install Airsonic` to open the NSSM service installer and start setting up the Windows Service for Airsonic.

Set the following settings in the `Application` tab.

- Path = `java.exe`
- Startup directory = `C:\Program Files\Airsonic`
- Arguments = `-Dairsonic.home="C:\\Program Files\\Airsonic" -Dserver.port=4040 -jar airsonic.war`

Open the `Shutdown` tab and remove all the flags in the checkboxes excepted the one for `Generate Contral-C`.

That's it, you're ready to start the Windows Service by typing `net start Airsonic` in your command prompt or PowerShell console.

Wait some seconds and browse to `http://localhost:4040`. You should see the Airsonic logon page.

##### Run the Airsonic Windows Service under a Service Account

The Airsonic Windows Service we just setup, is now running under the `SYSTEM` account. This is just fine, if all your media are on a local disk. But if you have your media on a network share, e.g. on a NAS you need to be able to connect to this share. To access this share you need to have the propper permissions. To achieve that, you should run your Airsonic under a service account and not by using the `SYSTEM` account. In this instructions this service account is called `sa-airsonic`.

> **NOTE**: Of course you can choose another name then `sa-airsonic` if you like.

Running the Airsonic Windows Service using another (non-admin) user also improves the safety of your system, as the `SYSTEM` account has way more rights then really needed to run Airsonic.

If you have an Active Directory domain you can create a new user in the Active Directoy with the name `sa-airsonic`. Afterwards you have to grant this user permissions on you network share/drive.

If you don't have an Active Directory and running your computers in a workgroup (non-domain environment), you need to create a new Windows user called `sa-airsonic`. Make sure to create this user account with the same name and password on your device with the network share as well.

The `sa-airsonic` user doesn't necessarily have to get the `Administrators` group/permissions, the `Users` group is just fine. 

After you created the `sa-airsonic` user, we need to change folder permission and the Windows Service to use this account. 

- Grant `Modify` permissions for `sa-airsonic` on `C:\Program Files\Airsonic`.

- Open `services.msc` and lookout for the `Airsonic` service. Stop this service and open the properties. In the `Log On` tab change the settings to use `This account` and specify the username and password of your `sa-airsonic` account. This should automatically grant `Log on as a service` permission for `sa-airsonic` on your computer.

Now you're ready to start the `Airsonic` service again and you should be able to add your network drive as media folder in the Airsonic settings under `Media folders` by using a UNC path like `\\servername\share\folder`.
