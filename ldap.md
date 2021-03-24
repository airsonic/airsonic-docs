---
layout: docs
title: Setting up a LDAP configuration
permalink: /docs/ldap/
---

#### Connect to an existing LDAP server

Airsonic can use an LDAP server as an identity provider. To do this, you need to already have an LDAP server, and know the following details:

  - The address of the server and the port.
  - The base DN under which all of your users are (if you don't know, it can be the whole LDAP directory, e.g. `dc=example,dc=com`).
  - The credentials of a user who can read your LDAP (usually not the regular users). It can either be the LDAP manager's credentials, or a dedicated read-only user (recommended). If your server allows anonymous lookups, then you don't need this.

##### From the UI

###### LDAP URL

Go to Settings &rarr; Advanced. At the bottom of the section, tick "Enable LDAP authentication". For the LDAP URL, add `ldap://<your LDAP server>:<port>/<base DN>`. For instance, it could look like: `ldap://ldap.example.com:389/dc=example,dc=com`.

The port is `389` by default, or `636` if you use SSL (then you need `ldaps://` at the beginning).

All the users should be under the base DN provided.

###### LDAP search filter

The LDAP search filter is the field you're going to match the logins against.  By default it's `(sAMAccountName={0})` for Microsoft Active Directory, but another popular setting is `(uid={0})` for OpenLDAP servers (it mostly depends on your internal user representation).

###### LDAP search filter

If your server doesn't allow anonymous lookup, you need a reader DN and password, to fill here. It can be `cn=admin,dc=example,dc=com`, or a readonly user (e.g. `cn=readonly,dc=example,dc=com` if you have one).

###### Automatically create users

You can chose to either limit Airsonic access to the users who already have an account on Airsonic, or to allow anyone in your LDAP to access Airsonic (in that case, tick the box). Note that if you want group-based control, you can check group membership as described in the [Advanced section](#advanced).

##### From the airsonic.properties file

The relevant properties section looks like this:

```
LdapEnabled=true
LdapUrl=ldap://example.com:389/ou=people,dc=example,dc=com
LdapSearchFilter=(sAMAccountName={0})
LdapManagerDn=cn=admin,dc=example,dc=com
LdapManagerPassword=XXXXXXXX
LdapAutoShadowing=true
```

As described above, `LdapUrl` contains the URL:port of your LDAP server, and the base DN for the user lookup. `LdapSearchFilter` contains the expression to find a user. `LdapManagerDn` and `LdapManagerPassword` are the credentials of the readonly or admin account. And finally, if `LdapAutoShadowing` is true, any user with an account in the LDAP server can log in to Airsonic.

Note that the password is unencrypted, but converted to hexadecimal. You can convert both ways on various websites such as [onlineutf8tools.com](https://onlineutf8tools.com/convert-hexadecimal-to-utf8).  As such, make sure access to your `airsonic.properties` file is well controlled (and not checked in on GitHub).

##### Advanced

The LDAP search filter can be used for more than just looking up the user. You can also filter on group membership, allow login by email or more.

###### Group management

To lookup whether a user is in a group, one possibility is to have your LDAP structure use `groupOfNames` or `groupOfUniqueNames`. They provide the `memberof` function, which can be used like so:

```
LdapSearchFilter=(&(uid={0})(memberof=CN=airsonicUsers,OU=Users,DC=example,DC=com))
```

Note the `&` at the beginning: it means check one AND the other.

###### Login by email

If you want to allow your users to login via either user name or email, you can use an LDAP property for that:

```
LdapSearchFilter=(|(uid={0})(mail={0}))
```

Note the `|` at the beginning: it means check one OR the other.

###### Putting it together

To allow login via either `uid` or email, but only if the user is in a group, you can do:

```
(&
    (|
        (uid={0})
        (mail={0})
        (primaryMail={0})
    )
    (memberof=CN=airsonicUsers,dc=example,dc=com)
)
```

Of course you have to pack all of that on a single line, like so:

```
LdapSearchFilter=(&(|(uid={0})(mail={0})(primaryMail={0}))(memberof=CN=airsonicUsers,dc=example,dc=com))
```
