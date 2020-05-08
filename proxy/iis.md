---
layout: docs
title: Setting up IIS
permalink: /docs/proxy/iis/
---

> **NOTE**: This manual was made by using Windows Server 2012 with IIS 8. For other versions it might be slightly different.

> **NOTE**: This manual was made by running Airsonic NOT under a custom context path (the default value is `/`).

#### IIS installation

Open the `Server Manager` and start the `Add Roles and Features` wizard from the `Manage` menu. From there install the Windows Server Role `Web Server (IIS)`. If you like to use Airsonic Advanced, make sure you selected the `WebSocket Protocol` under `Application Development` as well.

To get the URL Rewrite module for IIS, you need to install the `Web Platform Installer`. You can download this installer from the [Microsoft Page](https://www.microsoft.com/web/downloads/platform.aspx).

After installing it, open the `Internet Information Services (IIS) Manager`. When you click on your server name on the left side, you'll see the `Web Platfrom Installer` in the management section. Install the newest `URL Rewrite` module.

#### IIS configuration

Configure the IP/hostname binding and certificate settings according to your wishes on the `Default Web Site` (or use a subfolder or create your own site). You'll find a lot of information about this on the internet for sure. If you don't have a public trusted certificate, just go for a self-signed one, that's completely fine.

Open the `URL Rewrite` settings from the `IIS` section under your `Default Web Site`.
Click on `View Server Variables...` and make sure you add the following variables:

```
HTTP_X_FORWARDED_HOST
HTTP_X_FORWARDED_PORT
HTTP_X_FORWARDED_PROTO
HTTP_X_FORWARDED_SERVER
ORIGINAL_HOST
```

As next you need to setup your rewrite rules. The easiest way to do so, is to just create the `web.config` by yourself. Open the folder of your IIS Site by making a right click on `Default Web Site` and choosing `Explorer`.
There you have to create a new file called `web.config` with the following content. If you already have the `web.config` there, merge the content.

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <clear />
                <rule name="Redirect Transfer" stopProcessing="true">
                    <match url="transfer" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false" />
                    <action type="Redirect" url="index" />
                </rule>
                <rule name="ReverseProxyInboundRule1" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false" />
                    <serverVariables>
                        <set name="HTTP_X_FORWARDED_HOST" value="airsonic.public.url" />
                        <set name="HTTP_X_FORWARDED_SERVER" value="airsonic.public.url" />
                        <set name="HTTP_X_FORWARDED_PROTO" value="https" />
                        <set name="HTTP_X_FORWARDED_PORT" value="443" />
                        <set name="ORIGINAL_HOST" value="localhost" />
                    </serverVariables>
                    <action type="Rewrite" url="http://localhost:4040/{R:1}" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>
```

Make sure you set `airsonic.public.url` to the public URL of your Airsonic site. Also update the internal name `localhost` and the port `4040` if needed.

> **NOTE**: You probably don't need the `Redirect Transfer` rule, when you run Airsonic under a custom context path.
