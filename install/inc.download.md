#### Download Airsonic WAR package

Download the latest Airsonic .war package from the [download page](/download), or with the command below:

```
wget {{ site.repo }}/download/v{{ site.stable_version }}/airsonic.war
```

Download and import [`Andrew DeMaria`](https://github.com/muff1nman) public key:

```
gpg --keyserver keyserver.ubuntu.com --recv 0A3F5E91F8364EDF
```

Download the signed key file and verify the previously download .war package:

```
wget {{ site.repo }}/download/v{{ site.stable_version }}/airsonic.war.asc
gpg --verify airsonic.war.asc
```
