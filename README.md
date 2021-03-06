<!-- markdownlint-disable first-line-h1 ol-prefix -->

[![Build Status](https://travis-ci.org/MaxMilton/docker-chromium.svg?branch=master)](https://travis-ci.org/MaxMilton/docker-chromium) [![Image version](https://images.microbadger.com/badges/version/maxmilton/chromium.svg)](https://microbadger.com/images/maxmilton/chromium) [![Image layers](https://images.microbadger.com/badges/image/maxmilton/chromium.svg)](https://microbadger.com/images/maxmilton/chromium)

# Chromium In A Container

Launch an ephemeral chromium instance in a Docker container. Useful for times you need to quickly launch a browser that's completely fresh, e.g. web page testing or visiting suspect sites. By default, it's likely this will only run on a Linux desktop with X11 compatibility (e.g. Wayland + `xwayland`).

Based on the [amazing work done by jessfraz](https://github.com/jessfraz/dockerfiles/blob/master/chromium/Dockerfile) but with much customisation.

Uses a very opinionated [default configuration](https://github.com/MaxMilton/docker-chromium/blob/master/default.conf) for performance and security.

## Usage

### Build

```bash
docker build -t local/chromium .
```

Or, optionally you can download a pre-built image from Docker Hub (you'll also need to edit the `launch.sh` script):

```bash
docker pull maxmilton/chromium
```

### Run

_NOTE: The default page is `about:blank` for fast launch time._

```bash
./launch.sh
```

You can optionally pass an alternate Docker command:

```bash
./launch.sh http://localhost:8080 --allow-insecure-localhost
```

## Enabling persistence

> TL;DR — edit: `launch.sh`; uncomment code.

When run as is, the docker container is ephemeral so each time you launch an instance it's a completely fresh browser (useful for testing or as an incognito mode alternative). Browser data persistence is not enabled by default.

To make the container data persist edit the `launch.sh` file by commenting out the existing `docker run` and uncommenting the persistent docker run code. You can now sign in to create a profile etc.

The differences are:

1. Prevent removing container after exiting, so we don't need:

```bash
  --rm \
```

2. Mount host volumes to store the data:

_NOTE: The `:z` sets the correct SELinux role and allows read/write access._

```bash
  --volume "$HOME"/Downloads:/home/chromium/Downloads:z \
  --volume "$HOME"/.config/chromium/:/data:z \
```

## Additional considerations

1. Bug: Audio is sent to the default audio device and is not easily configurable.

2. Uses a custom set of chromium flags for improved security and performance: `default-flags`.

3. `--volume /dev/shm:/dev/shm` is necessary because Docker currently only allocates 64 MB of memory to /dev/shm but chromium needs a lot more to run without crashing. On some systems it my not be required. [More info](https://github.com/c0b/chrome-in-docker/issues/1).

## Licence

Released under the MIT licence; see [LICENCE](https://github.com/MaxMilton/docker-chromium/blob/master/LICENCE).

-----

© 2017 [We Are Genki](https://wearegenki.com)
