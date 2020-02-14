---
layout: docs
title: Installing Airsonic on FreeBSD and FreeNAS
permalink: /docs/install/example/freebsd-freenas/
---
#### Preamble

This guide will walk you through the process of deploying Airsonic on FreeBSD either in a [Jail](https://www.freebsd.org/doc/handbook/jails.html) on on the main system. Unless you are an advanced user, you will want to install Airsonic inside of a jail. FreeNAS has excellent support for jails, but you can also use a jail manager like [IOcage](https://iocage.io/). The prerequisites are you have root access on your FreeBSD machine (or jail), the ip address of the machine (or jail) and the Airsonic war available at the [Airsonic github page](https://github.com/airsonic/airsonic/releases).

If on FreeNAS create a standard jail in the web interface and enter the shell. Make sure you give it an IP 
address that is similar to your computer's IP address or DHCP. 

If using iocage or other jail manager, follow the instructions to create a new jail and assigning an IP 
address that can be seen outside of the host OS.

### Download the Airsonic package

Navigate to the Airsonic Releases page ([https://github.com/airsonic/airsonic/releases/](https://github.com/airsonic/airsonic/releases/)) and find the "Airsonic.war" link under the most recent release Right-click and "Copy link location", we will need this later.

Open a shell in your jail and download Airsonic using fetch(1):
```
fetch https://github.com/airsonic/airsonic/releases/download/v10.5.0/airsonic.war
```
You should now see airsonic.war in your home (~/) directory.
```
root@airsonic-jail:~ # ls -la ~/
drwxr-xr-x  10 root  wheel        26 Feb  5 09:06 .
drwxr-xr-x  19 root  wheel        24 Feb  5 05:17 ..
drwxr-xr-x   3 root  wheel         3 Aug 22  2018 .config
-rw-r--r--   2 root  wheel       957 Jun 29  2018 .cshrc
-rw-------   1 root  wheel     40014 Feb  5 09:06 .history
-rw-r--r--   1 root  wheel  77345243 Feb  5 09:05 airsonic.war
```

### Install Tomcat

Airsonic is a Java application, so we need a Java server to run it in. Tomcat is a free, lightweight Java 
web server that can be used to simplify our stack, as it's a "batteries included" solution.

Open a shell in your Airsonic jail, then install Tomcat 8.5 and the Nano text editor.
```
pkg install tomcat85 nano
```
Accept the prompts complete installation.

In BSD-like distrobutions, non-base (read: not included) software is installed to /usr. When installing 
from the pkg respository, Tomcat will install to /usr/local/
**For Tomcat 8.5***:
```
/usr/local/apache-tomcat-8.5
```

#### Configure Tomcat

With Tomcat installed, we can begin configuring it to suit our needs.

We will use the "built-in" nature of installing from pkg and add a few lines to our *rc.conf* file 
using the sysrc utility.
```
# sysrc tomcat85_enable=YES
tomcat_enable: NO -> YES
```
You can verify the changes have been made by reading the /etc/rc.conf file:
```
root@airsonic-jail:~ # cat /etc/rc.conf
...
tomcat85_enable="YES"
...
```

Most music lovers interested in Airsonic likely have music with unicode (non-english) characters, 
so we'll add support for Tomcat to read them by adding some *environment variables*.
> ***NOTE*** Please change en_US.UTF-8 and Europe/Paris to suit your needs. If your system time matches 
the timezone you wish Airsonic to operate in, you may omit the *TZ* variable.
```
# sysrc tomcat85_env="LANG=en_US.UTF-8 TZ=Europe/Paris"
tomcat85_env:  -> LANG=en_US.UTF-8 TZ=Europe/Paris
```

By default, Tomcat will serve its content from a context directory. Airsonic will appear like:
```
http://10.0.0.50:8080/airsonic
```
Because we have a nice jail environment just for Airsonic, we'll make a change that allows Airsonic 
to be served as the root context.

Open the server.xml configuration file:
```
# nano /usr/local/apache-tomcat-8.5/conf/server.xml
```
Find the following element:
```
<Host name="localhost" appBase="webapps"
    unpackWARs="true" autoDeploy="true"> 
```
Add the line *<Context docBase="airsonic" path="" reloadable="true" />* below the element, before the
</Host> tag; then save:
```
<Host name="localhost" appBase="webapps"
	unpackWARs="true" autoDeploy="true">
    <Context docBase="airsonic" path="" reloadable="true" />
```

#### Create directories and set up permissions

Create directories and set up permissions:

```
mkdir /var/airsonic
chown -R www:www /var/airsonic
chown -R www:www /usr/local/apache-tomcat-8.0/webapps
```

#### Start Tomcat

We want to start Tomcat the same way the system will, so we'll use the service command:
```
# service tomcat85 start
Starting tomcat85.
```
> If you encounter an error here, you may have missed adding the enable variable to your rc.conf
> Head back to [Configure Tomcat](#configure-tomcat) if you run into this:
> ```
# service tomcat85 restart
Cannot 'restart' tomcat85. Set tomcat85_enable to YES in /etc/rc.conf or use 'onerestart' instead of 'restart'.
```

#### Test Tomcat
If Tomcat started without any issues, lets test if Tomcat is listening on port 8080:
```
netstat -an | grep 8080
```
It should return a line containing the IP address of your system (or jail).

```tcp4	0	0 10.0.0.10.8080	-.-	LISTEN ```

> If in a jail it may also return the line "netstat: kvm not available: /dev/mem: No such file or directory" This can be ignored.

We should be able to navigate to the IP address given to us in the *netstat* command output, but 
we can also use ifconfig to find the address:
```
# ifconfig | grep inet
        inet 10.0.0.10 netmask 0xffffff00 broadcast 10.0.0.255
```

When we browse to http://10.0.0.10:8080/manager, we should see "403 Access Denied"!
Congratulations, you now have Tomcat ready to deploy Airsonic!

#### Deploy Airsonic

Now we can copy our airsonic.war downloaded [previously](#download-the-airsonic-package), 
placing it into our webapps/ directory.
```
cp ~/airsonic.war /usr/local/apache-tomcat-8.5/webapps/
```
We need to make sure the *www* user can read the Airsonic package, so we'll change ownership 
and restart Tomcat to start the deployment:
```
# chown www:www /usr/local/apache-tomcat-8.5/webapps/airsonic.war
# service tomcat85 restart
Stopping tomcat85.
Waiting for PIDS: 26737.
Starting tomcat85.
```
> ***NOTE*** You may see that Tomcat has failed to start because it took longer to unpack 
> Airsonic than the *service* command expected it to take. You can double check the Tomcat 
> log to make sure things are still happening.
> ```
# tail -n 500 /usr/local/apache-tomcat-8.5/logs/catalina.out
06-Feb-2020 03:45:53.544 INFO [main] org.apache.coyote.AbstractProtocol.init Initializing ProtocolHandler ["http-nio-8080"]
06-Feb-2020 03:45:53.552 INFO [main] org.apache.tomcat.util.net.NioSelectorPool.getSharedSelector Using a shared selector for servlet write/read
06-Feb-2020 03:45:53.563 INFO [main] org.apache.coyote.AbstractProtocol.init Initializing ProtocolHandler ["ajp-nio-8009"]
06-Feb-2020 03:45:53.564 INFO [main] org.apache.tomcat.util.net.NioSelectorPool.getSharedSelector Using a shared selector for servlet write/read
06-Feb-2020 03:45:53.565 INFO [main] org.apache.catalina.startup.Catalina.load Initialization processed in 386 ms
06-Feb-2020 03:45:53.588 INFO [main] org.apache.catalina.core.StandardService.startInternal Starting service [Catalina]
06-Feb-2020 03:45:53.588 INFO [main] org.apache.catalina.core.StandardEngine.startInternal Starting Servlet Engine: Apache Tomcat/8.5.49
           _                       _
     /\   (_)                     (_)
    /  \   _ _ __  ___  ___  _ __  _  ___
   / /\ \ | | '__|/ __|/ _ \| '_ \| |/ __|
  / ____ \| | |   \__ \ (_) | | | | | (__
 /_/    \_\_|_|   |___/\___/|_| |_|_|\___|
                        10.5.0-RELEASE
2020-02-06 03:45:59.203  INFO --- org.airsonic.player.Application          : Starting Application v10.5.0-RELEASE with PID 26943 (/usr/local/apache-tomcat-8.5/webapps/airsonic/WEB-INF/classes started by root in /)
```


#### Navigate to Airsonic

In a browser. Take your `SERVER_IP` and `PORT` and found in [Test Tomcat](#test-tomcat), 
and navigate there in a web browser

> In this example install, we would browse to:
> ```http://10.0.0.10:8080```

#### Log into Airsonic

Log in. The default is username: admin password: admin

Follow the prompts on the web page to change the password. This will log you out. Please re-login with your new password

#### Configure media directories in system or jail

If you are using FreeNAS, you will mount your media files using the FreeNAS gui.

For IOcage or other Jail management systems, you will need to follow the documentation for that 
jail manager. 

Most users will find a [nullfs](https://www.freebsd.org/cgi/man.cgi?query=mount_nullfs) mount of an existing directory or mount on their filesystem to be the 
most simple solution for allowing Airsonic to read your media inside of a jail. This will bring 
the specified directory into the jail, and can easily be set to read only for a safer implementation.

> ***IOcage example*** IOcage uses the same fstab mechanism that your host system does. To allow 
> Airsonic read only access to a directory on the host system inside of it's jail, we can add 
> the following to that jails *fstab* configuration file.
>
> In our example, we have a directory at /storage/media/music that we will mount into the jail 
> at the same location. This confiruation will be done outside of the jail.
>
> ```nano /iocage/jails/airsonic-jail/fstab```
>
> At the bottom of the file, we can add some white space and include our custom mount.
> ```
/storage/media/music	/iocage/jails/airsonic-jail/root/storage/media/music	nullfs ro	0	0
```
> Once the *fstab* file is saved, you can restart your jail and enter a shell, where you should 
> be able to see your files.

##### Configure media directories in Airsonic

In Airsonic click `2. Setup Media folders`.

Name your media folder and put in the path to your music. For our example, this would be 
`/storage/media/music` 

Once the media folder is saved to settings, click `Scan media folders now`

Congratulations you have set up Airsonic!

### Advanced configuration

#### Transcoding setup

If you want transcoding and DON'T need mp3 support:

```
pkg install ffmpeg
ln -s /usr/local/bin/ffmpeg /var/airsonic/transcode/ffmpeg
service tomcat85 restart
```

Congratulations you have transcoding enabled!

If you need mp3 support and most likely you will, the process is more arduous as FreeBSD's ffmpeg 
doesn't contain mp3 support by default and must be configured and compiled by the user.

##### Install ffmpeg from source

It is recommended to create an entirely separate jail from your Airsonic jail to build ffmpeg from source.

Once your new jail is created, and is able to connect to the internet, open a shell and 
we'll use the portsnap tool to pull down the ports tree.
```
portsnap fetch
portsnap extract
```

We need to install build & run dependencies, so we'll start by hopping into the ffmpeg directory
```
# cd /usr/ports/multimedia/ffmpeg
```
Once there, we can use the *build-depends-list* option to get a list of build dependencies
```
# pkg install `make build-depends-list | rev | cut -d'/' -f-1 | rev | cut -d'.' -f-1`
```
> ***For interested parties*** running command(s) in side of a back-tick \`\` will evaluate what's 
> inside before passing the result to the command outside of the back-tick. Here, we're telling 
> the shell to run *make build-depends-list* and performing some cut magic to turn a string like 
> */usr/ports/lang/perl5.30* into a package name pkg can use, *perl5*. 

We also need to use the *run-depends-list* to grab all of our runtime dependencies and install them.
```
# pkg install `make run-depends-list | rev | cut -d'/' -f-1 | rev | cut -d'.' -f-1`
```
***This is some magic, and it may not work all the time.***

##### Build ffmpeg & lame

Now that we have our depandancies installed, we can build our ffmpeg package. Assuming we are already 
in the /usr/ports/multimedia/ffmpeg directory:
```
# make configure
```

This will bring up a menu. Scroll down using arrow keys to "LAME" and hit the spacebar to enable it. Press enter to continue.

The ffmpeg source files will automatically be downloaded then you will be presented with an additional prompt to install documentation. I uncheck with spacebar then press enter to continue.

Start build and installation of ffmpeg
```
# make package
```
***Building ffmpeg will take some time depending on the capabilities of your machine, please be patient.***

We will now build the LameMP3 package the same way:
```
# cd /usr/ports/audio/lame
/usr/ports/lame # make package
```

Now that we have a package like *ffmpeg-4.2.2_5,1.txz* and *lame-3.100_2.txz* in *work/pkg*, we can 
install it using *pkg*. Since you (hopefully) have done everything in a clean/new jail, we'll need to move 
this package to your Airsonic jail. The easiest way is to copy it between jails on the host system:
```
# cd /iocage/jails
/iocage/jails # cp build-ffmpeg/root/usr/ports/multimedia/ffmpeg/work/pkg/ffmpeg-4.2.2_5,1.txz airsonic-jail/root/root/
/iocage/jails # build-ffmpeg/root/usr/ports/audio/lame/work/pkg/lame-3.100_2.txz airsonic-jail/root/root/
```

Once the packages are successfully copied to your Airsonic jail, you should see it in your 
root user home directory. Open a shell on your Airsonic Jail and install the packages. 
ffmpeg requires lame, so install lame first.
```
# pkg add /root/lame-3.100_2.txz
Installing lame-3.100_2...
Extracting lame-3.100_2: 100%

# pkg add /root/ffmpeg-4.2.2_5,1.txz
Installing ffmpeg-4.2.2_5,1...
Extracting ffmpeg-4.2.2_5,1: 100%
```

Because we don't want the pre-packaged ffmpeg to overwrite our custom ffmpeg package, we 
will lock the packages:
```
# pkg lock ffmpeg
ffmpeg-4.2.2_5,1: lock this package? [y/N]: y
Locking ffmpeg-4.2.2_5,1
# pkg lock lame
lame-3.100_2: lock this package? [y/N]: y
Locking lame-3.100_2
```

Make sure you've got proper permissions on the ffmpeg executable:
```
# chown www:www /usr/local/bin/ffmpeg
```

Symlink ffmpeg to where Airsonic expects the transcoder to be.
```
ln -s /usr/local/bin/ffmpeg /var/airsonic/transcode/ffmpeg
```

Finally restart tomcat
```
# service tomcat85 restart
```

Congratulations you have ffmpeg with mp3 support installed ready for Airsonic to use!

#### Enable Tomcat web administration gui

Edit Tomcat's user configuration file with your favourite text editor. We installed nano in step 1.

```
rm /usr/local/apache-tomcat-8.0/conf/tomcat-users.xml
nano /usr/local/apache-tomcat-8.0/conf/tomcat-users.xml
```

Copy/paste the following:

```
<tomcat-users xmlns="http://tomcat.apache.org/xml"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
version="1.0">

<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<role rolename="admin-gui"/>
<role rolename="admin-script"/>
<user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script"/>

</tomcat-users>
```

> **NOTE**: If you wish to use a different username and password please append the second last line to contain your preferred username and password.
> ```
> <user username="yourusername" password="yourpassword" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script"/>
> ```