Nimble streaming server
=======================

This container provides a [nimble](https://wmspanel.com/nimble) streaming server listening on ports **8081** (http) and **1935** (RTMP).

Nimble is "freemium" software, it is freely distributed but must be managed through the [WMSPanel Portal](https://wmspanel.com/). You will need to get an account to be able to use this application.

Usage
-----

From Docker registry:

```
docker pull botsunit/nimble
```

Or build yourself:

```
git clone https://github.com/bostunit/docker-nimble.git
docker build --rm -t bostunit/nimble docker-nimble
```

To run Nimble streamer, you first need to register a server with WMSPanel.

To do so you the easiest way is to run this image with a new, empty volume for `/etc/nimble`:

```
docker run --rm \
    -e WMSPANEL_USER=your@user.name \
    -e WMSPANEL_PASS=your_password  \
    -v nimble-data:/etc/nimble
    bostunit/nimble
```

The image will detect that `/etc/nimble` is empty and it will both register a server in WMSPanel
and save its identifying information in `/etc/nimble/nimble.conf`. Because we asked Docker to create
a `nimble-data` volume and mount it at `/etc/nimble`, when the container exits and is removed the
contents of `/etc/nimble` will persist.

Once the volume has been initialized, you can start the Nimble server itself with:

```
docker run -d --name nimble \
    -v nimble-data:/etc/nimble
    bostunit/nimble
```

Configuration
-------------

All configuration is managed through the WMSPanel portal. The first time you run the container, you must provide your portal credentials to have the server register itself. You provide the credentials through the following environment variables:

  - **WMSPANEL_USER**: WMSPanel portal username, e.g. your@email.com
  - **WMSPANEL_PASS**: WMSPanel password.
  - **WMSPANEL_SLICES**: Optional, list of slices to register this server to.

The container registers the server and stores all configuration settings under `/etc/nimble/`. You may want to have that path mounted from an external volume.

Once `/etc/nimble/nimble.conf` is populated with the right account and key data, you no longer need to provide the username and password as environment variables to the container.

If you change your username and/or password, and need to register the server again, you should remove the `/etc/nimble/nimble.conf` file and restart the container, providing the new user and pass.

You can edit `/etc/nimble/nimble.conf` to set a few parameters, see http://blog.wmspanel.com/p/nimble-streamer-configuration.html for documentation.

Volumes
-------

You may want to mount to external volumes to the following paths:

  - `/etc/nimble`: This is the configuration folder. It is bootstrapped the first time you run the container with a WMSPanel username and password.
  - `/var/cache/nimble`: This is used to save temporary files and stats.

Ports
-----

The container exposes ports **8081** (HTTP) and **1935** (RTMP)
