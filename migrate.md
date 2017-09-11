---
layout: docs
title: Migrating from Subsonic to Airsonic
permalink: /docs/migrate/
---
This guide helps you to migrate your data from Subsonic to Airsonic. It has been tested with Subsonic 5 to Airsonic 6.

#### Install Airsonic

Install Airsonic as described in one of the following install guides:

- [WAR package (Tomcat)](/docs/install/war)
- [WAR package (Standalone)](/docs/install/war-standalone)
- [Docker container](/docs/install/docker)
{% comment %} Build targets not suported yet

- [Deb package (Debian / Ubuntu)](/docs/install/deb)
- [Rpm package (Red Hat / Fedora)](/docs/install/rpm)
- [Exe package (Windows)](/docs/install/exe)
- [Pkg package (macOS)](/docs/install/pkg)

{% endcomment %}
- [Build from source](/docs/install/source)

#### Migrate to Airsonic

After installation of Airsonic, the database needs to be migrated. In preperation for that, stop the Airsonic service.

If you ran Subsonic before, your data will be (by default) stored in `/var/subsonic`. Assuming you did not use Airsonic before, we will delete all data from Airsonic:

> WARNING: Deletes all Airsonic data
```
sudo rm -R /var/airsonic
```

We then copy Subsonic data to Airsonic location. Be aware that a couple of files need to be renamed:

```
sudo cp -a /var/subsonic /var/airsonic
sudo mv /var/airsonic/subsonic_sh.log airsonic_sh.log
sudo mv /var/airsonic/subsonic.log airsonic.log
sudo mv /var/airsonic/subsonic.properties airsonic.properties
sudo mv /var/airsonic/db/subsonic.backup /var/airsonic/db/airsonic.backup
sudo mv /var/airsonic/db/subsonic.data /var/airsonic/db/airsonic.data
sudo mv /var/airsonic/db/subsonic.lck /var/airsonic/db/airsonic.lck
sudo mv /var/airsonic/db/subsonic.log /var/airsonic/db/airsonic.log
sudo mv /var/airsonic/db/subsonic.properties /var/airsonic/db/airsonic.properties
sudo mv /var/airsonic/db/subsonic.script /var/airsonic/db/airsonic.script
```

Change the `/var/airsonic` owner to use that will run airsonic (e.g. tomcat8 or airsonic):

```
sudo chown -R user:user /var/airsonic
```

Then start Airsonic service again.

Your old settings will still be there. **If you wish**, you can delete subsonic data:

```
sudo rm -R /var/subsonic
```
