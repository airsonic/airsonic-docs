---
layout: docs
title: Docker Installation
permalink: /docs/install/docker/
---
This page describes how to run the official [Airsonic Docker image](https://hub.docker.com/r/airsonic/airsonic/).

##### Prerequisites

In order to install and run Airsonic using [Docker](https://www.docker.com/), a working installation of Docker is required.

If you do not have Docker installed, you may follow [these instructions](https://docs.docker.com/engine/installation/) to install it on your system.

Verify that Docker is installed by running
```
$ docker -v
Docker version 17.06.0-ce, build 02c1d87
```

##### Running Airsonic

Running the Airsonic Docker container is straight forward. Simply execute the command below.

```
docker run -p 4040:4040 -d airsonic/airsonic
```

You should be able to access Airsonic on [http://localhost:4040](http://localhost:4040) after a couple of seconds.

> When running Docker this way all changes you make to Airsonic will be lost when stopping the container. Use [Volumes](#persisting_data) to persist data when the container is stopped.

##### Persisting Data

The Airsonic Docker file provides various mount points for volumes. You can see which by checking out the [Dockerfile](https://github.com/airsonic/airsonic/blob/master/install/docker/Dockerfile).

Attach volumes to your docker container when starting the container like so:

```
docker run \
  -v data:/airsonic/data \
  -v music:/airsonic/music \
  -v playlists:/airsonic/playlists \
  -v podcasts:/airsonic/podcasts \
  -p 4040:4040 \
  -d \
  airsonic/airsonic
```

You can find additional information regarding volumes [here](https://docs.docker.com/engine/admin/volumes/volumes/). Inspect volumes by running, for example `docker volume inspect data`.

##### Advanced Configuration

Configuring Airsonic is best done using the web-interface.

Regardless, you may at one point wish to configure Airsonic using the [properties file](/docs/configure/airsonic-properties).

You may adjust the `airsonic.properties` file directly. You can find it in the data volume that was attached to the container. Before making any changes to the file make sure that the Airsonic container is stopped.

If you did not supply another volume mountpoint the file will reside in `/var/lib/docker/volumes/data/_data`. You need administrator rights to modify it.

```
sudo nano /var/lib/docker/volumes/data/_data/airsonic.properties
```

Another way to configure Airsonic is by passing start-up arguments to the container when executing `docker run`. You may use the environment variable `JAVA_OPTS` to pass properties to Airsonic.

```
docker run \
  -p 4040:4040 \
  -e JAVA_OPTS="-DDatabaseMysqlMaxlength=512 -DDatabaseConfigType=embed ..." \
  airsonic/airsonic
```

View the docker container [start-up script](https://github.com/airsonic/airsonic/blob/master/install/docker/run.sh) for additional information.
