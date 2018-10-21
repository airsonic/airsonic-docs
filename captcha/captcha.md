---
layout: docs
title: CAPTCHA
permalink: /docs/captcha/
---
Airsonic supports requiring anybody attempting to reset a user's password to
solve a [CAPTCHA](https://en.wikipedia.org/wiki/CAPTCHA), making it more
difficult for attackers to automatically cause passwords to be reset. Versions
10.1.2 and older supported this by default, but did so using a service that
stopped working at the end of March 2018 (making it impossible for users to
reset their passwords).

The new CAPTCHA support is disabled by default because it requires additional
configuration. This documentation will walk through the process.

## Configuring

The settings controlling CAPTCHA use can be found in the advanced settings pane
of the Airsonic web interface.

![A screenshot of the settings page, containing a "require CAPTCHA for account
recovery" checkbox which is unchecked, and two text entry fields for a reCAPTCHA
site and secret key which are both empty.](captcha-settings.png)

Checking the box will cause the CAPTCHA to be shown on the password reset page;
if the site and secret keys are not provided Airsonic will use default testing
keys which will cause a warning to be shown on the CAPTCHA and make all
verifications pass. While this configuration is not any more secure, the mere
presence of a CAPTCHA (even if it does nothing) may deter some unsophisticated
attackers.

To obtain reCAPTCHA keys it is necessary to register with Google, at
https://www.google.com/recpatcha/admin. Register a new site with any label (the
label merely identifies a site in the admin console) and select "reCAPTCHA v2"
as the type.

![A screenshot of a sample reCAPTCHA registration form. The label field reads
"My Airsonic Site" and the radio button next to "reCAPTCHA v2" is
selected.](captcha-registration.png)

After registering, the reCAPTCHA admin panel will show site and secret keys.
Copy these into the Airsonic settings; the other information for client and
server-side integration is unnecessary because Airsonic already implements those
integrations.

## Testing

It is possible to test CAPTCHA configuration by logging out of Airsonic and
selecting "Forgotten your password?" on the login page. If the CAPTCHA is
enabled and correctly configured, the page should include a "I'm not a robot"
widget like below.

![A form to enter an email address and send a new password, with a reCAPTCHA
widget below containing a checkbox and the label "I'm not a
robot."](captcha-in-situ.png)
