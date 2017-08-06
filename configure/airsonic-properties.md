---
layout: docs
title: Configure Airsonic properties file
permalink: /docs/configure/airsonic-properties/
---
The `airsonic.properties` file parameters are simple key-value pairs stored in a list. It is recommended that these parameters are changed through the web interface settings page.

However, they can also be modified directly here.

- Shutdown your server first.
- Modify the `airsonic.properties` (which can be found in your `airsonic.home` folder).
- Restart it for changes to take effect.

Here is a sample of the `airsonic.properties` file :

```sh
# Airsonic preferences.

JWTKey=XXXXXXXXXXXXXXXXXXXXXXX
SettingsChanged=XXXXXXXXXXXXXXXXXXXXXXX
MediaLibraryStatistics=1512 4850 62662 486912890569 15485585
LastScanned=XXXXXXXXXXXXXXXXXXXXXXX
IndexString=A B C D E F G H I J K L M N O P Q R S T U V W X-Z(XYZ)
IgnoredArticles=The El La Los Las Le Les
Shortcuts=New Incoming Podcast
PlaylistFolder=/var/playlists
MusicFileTypes=mp3 ogg oga aac m4a flac wav wma aif aiff ape mpc shn
VideoFileTypes=flv avi mpg mpeg mp4 m4v mkv mov wmv ogv divx m2ts
CoverArtFileTypes2=cover.jpg cover.png cover.gif folder.jpg jpg jpeg gif png
SortAlbumsByYear=true
GettingStartedEnabled=false
WelcomeTitle=Airsonic
WelcomeSubtitle=
WelcomeMessage2=
LoginMessage=
Theme=default
LocaleLanguage=en
LocaleCountry=
LocaleVariant=
IndexCreationInterval=3
IndexCreationHour=3
FastCacheEnabled=false
OrganizeByFolderStructure=true
DownloadBitrateLimit=0
UploadBitrateLimit=0
LdapEnabled=false
LdapUrl=ldap://host.domain.com:389/cn=Users,dc=domain,dc=com
LdapSearchFilter=(sAMAccountName={0})
LdapManagerDn=
LdapAutoShadowing=false
SmtpServer=smtp.mail.net
SmtpEncryption=SSL/TLS
SmtpPort=465
SmtpUser=info@exemple.net
SmtpFrom=info@exemple.net
SmtpPassword=XXXXXXXXXXXXXXXXXXXXXXX
```
