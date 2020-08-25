# rTorrent-ruTorrent [![Build Status](https://travis-ci.com/IAreKyleW00t/rtorrent-rutorrent-docker.svg?branch=master)](https://travis-ci.com/IAreKyleW00t/rtorrent-rutorrent-docker)

Available on [GitHub](https://github.com/IAreKyleW00t/rtorrent-rutorrent-docker) and [DockerHub](https://hub.docker.com/r/iarekylew00t/rtorrent-rutorrent)

A small, simple, and lightweight Alpine-based Docker image built for `amd64`, `arm64`, and `arm`. Automatically rebuilt weekly to ensure updates from [Linuxserver.io](https://linuxserver.io) flow downstream.

### Features

- rTorrent
- ruTorrent
- irssi + ruTorrent plugin
- HTTPS **only**
- QuickBox theme (optional)
- Basic Authentication (optional)

## Usage

Various examples of how to use this image to get you going.

**NOTE** - Due to limitations with rTorrent/ruTorrent, any settings changed in the WebUI will **not** be saved after restarting the container.

### docker

```sh
docker run --name rtorrent-rutorrent \
    -v "${PWD}/data":/downloads \
    -v "${PWD}/config":/config \
    -p 8443:443 \
    -p 51415:51415 \
    -e PUID=1000 \
    -e PGID=1000 \
    --restart unless-stopped \
    iarekylew00t/rtorrent-rutorrent
```

### docker-compose

```yaml
version: "3"
services:
  rtorrent:
    image: iarekylew00t/rtorrent-rutorrent:latest
    restart: unless-stopped
    environment:
      TZ: America/New_York
      PUID: 1000
      PGID: 1000
    ports:
      - "8443:443"
      - "51415:51415"
    volumes:
      - ./data:/downloads
      - ./config:/config
```

### building locally

You can adjust `BUILD_CORES` to improve build times a bit.

```sh
git clone https://github.com/IAreKyleW00t/rtorrent-rutorrent-docker.git
cd rtorrent-rutorrent-docker
docker build --no-cache --pull \
    --build-arg BUILD_CORES=1 \
    -t iarekylew00t/rtorrent-rutorrent:latest \
    -f Dockerfile .
```

ARM variants can be built on `x86_64` hardware using the [multiarch/qemu-user-static](https://github.com/multiarch/qemu-user-static) Docker image.

    docker run --rm --privileged multiarch/qemu-user-static --reset -p yes

## Volumes, Ports, and Variables

Here for reference, but there isn't much.

| Name         | Description                          |
| ------------ | ------------------------------------ |
| `PUID`       | Running user UID                     |
| `PGID`       | Running user GID                     |
| `/config`    | Location for all configuration files |
| `/downloads` | Location for all torrent downloads   |
| `443`        | HTTPS port, ruTorrent WebUI          |
| `49160`      | DHT port                             |
| `51415`      | rTorrent network port                |

The default user that everything runs under is called `abc`, or has the UID and GID of `1000`. You can use your own UID/GID as needed.

## Basic Authentication

Basic authentication can be enabled by creating the file `/config/nginx/htpasswd` - you can use `htpasswd.example` as reference. This file will accept any htpasswd-compliant passwords, but a helper script is included in the container to create secure (and salted) passwords.

**While your container is running** you can use:

    docker exec -it rutorrent htpasswd-gen USERNAME PASSWORD

The output will be the text you can copy into your `htpasswd` file.

## Contributing

Contributions are welcome in the form of GitHub [Issues](https://github.com/IAreKyleW00t/rtorrent-rutorrent-docker/issues) and [Pull Requests](https://github.com/IAreKyleW00t/rtorrent-rutorrent-docker/pulls).

## License

This Docker container is open source and released under the terms of the [GPLv3 License](https://opensource.org/licenses/GPL-3.0).

## Credits

- [Linuxserver.io](https://linuxserver.io) for their base images
- [diameter/rtorrent-rutorrent](https://github.com/diameter/rtorrent-rutorrent) for a good starting point
- [QuickBox](https://github.com/QuickBox/club-QuickBox) for the ruTorrent theme
- [multiarch/qemu-user-static](https://github.com/multiarch/qemu-user-static) for mutli-architecture building capabilities
- [peterevans/dockerhub-description](https://github.com/peter-evans/dockerhub-description) for a way to update the Description on DockerHub
