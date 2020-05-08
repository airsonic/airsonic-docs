---
layout: docs
title: Release process
permalink: /docs/developer/release/
---

### Adding a new maintainer

The releases are signed by maintainer keys. In order to add a new maintainer,
you will need to follow the steps in this section.

#### Generating a new key

You will first need to generate (if you don't have one already) a new signing key. Full instructions
[here](https://help.github.com/en/github/authenticating-to-github/generating-a-new-gpg-key):

1. Generate a new signing key:

        $ gpg --full-generate-key                        # GPG >= 2.1.17
        $ gpg --default-new-key-algo rsa4096 --gen-key   # GPG < 2.1.17

2. Select RSA+DSA for the key type.

3. Select at least 4096 bits for the key size.

4. Select the expiry date (at most 3 years is a good idea).

5. Enter your user ID (email must be the same as the one you use to commit on Git).

#### Signing the new key

Making your new key signed by a maintainer is a good idea. This can be done
with the following steps:

1. Export your key:

        $ gpg --export --armor <key id> key.pem

2. Send the `key.pem` file to another maintainer, and ask them to run the
   following command, and then send the signed `key.pem` back to you:

        $ gpg --import --armor key.pem
        $ gpg --sign <key id>
        $ gpg --export --armor key.pem

#### Publishing the new key

Once generated, publish the key on the Git repository in the `install/keys`
directory as `<email>.pem`.  This directory contains GPG signing keys used to
sign and verify Airsonic releases, and can be used by anyone who wants to
validate releases.

It's also a good idea to publish it on some key servers:

    $ gpg --send-keys 262121A22A609283A6A46BCB7DEEDDBFC5A13AB4
    $ gpg --keyserver pgp.mit.edu --send-keys 262121A22A609283A6A46BCB7DEEDDBFC5A13AB4
    $ gpg --keyserver keyserver.ubuntu.com --send-keys 262121A22A609283A6A46BCB7DEEDDBFC5A13AB4

To export a key in the repo:

### Creating a release

These instructions come from [contrib/release.md](https://github.com/airsonic/airsonic/blob/master/contrib/release.md), but are copied here for easier access.

1. Ensure changelog is up to date

2. Create a new minor branch if not already exists. Checkout branch

        git checkout -b release-X.Y

3. Bump the maven pom

        mvn versions:set -DnewVersion=X.Y.Z-RELEASE

4. Commit maven pom changes


5. Create a new tag

        git tag -s vX.Y.Z -m 'Release vX.Y.Z' 

6. Package

        mvn clean verify -P docker

7. Sign sha256sums file

        gpg2 --clearsign airsonic-main/target/artifacts-checksums.sha

8. push up branch and tag

        git push origin vX.Y.Z
        git push -u origin release-X.Y

9. Create new release on github

   - Draft new Relase
   - Choose existing tag
   - Title is "Airsonic X.Y.Z"
   - Contents are the relevant entry of the CHANGELOG.md file
   - Upload `airsonic.war` and `artifacts-checksums.sha.asc`

10. Update latest docker tag

        docker tag airsonic/airsonic:X.Y.Z-RELEASE airsonic/airsonic:latest

11. Docker login with airsonic credentials in `airsonic-passwords` repo

        docker login

12. Push images

        docker push airsonic/airsonic:X.Y.Z-RELEASE
        docker push airsonic/airsonic:latest

13. Checkout master branch and bump maven version to next snapshot version

        git checkout master
        mvn versions:set -DnewVersion=X.Y+1.0-SNAPSHOT

14. Git commit and push

15. In the [airsonic.github.io repository](https://github.com/airsonic/airsonic.github.io/),
    update the current stable release in `_config.yml`:

        sed -i 's/^stable_version: .*$/stable_version: "X.Y.Z"/' _config.yml
