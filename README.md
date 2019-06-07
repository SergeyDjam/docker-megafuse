# docker-megafuse

This is an attemp to build an Alpine-based Docker image for access [Mega](https://github.com/meganz) volumes via FUSE, using [its SDK](https://github.com/meganz/sdk).

An image based on the [Matteo Serva's](https://github.com/matteoserva) [MegaFuse project](https://github.com/Amitie10g/docker-megafuse/tree/matteoserva) is also available at a [dedicated branch](https://github.com/Amitie10g/docker-megafuse/tree/matteoserva).

## Instructions

### Pull from Docker Hub
docker pull amitie10g/megafuse:latest

### Running
```
PUID=$(id -u)
PGID=$(id -g)

CONF_PATH=$HOME/config
CACHE_PATH=$HOME/cache

USERNAME=<MEGA username>
Password=<MEGA password>

mkdir -p $CONF_PATH $CACHE_PATH

docker run -t -i -d \
--name=megafuse \
-e PUID=$PUID \
-e PGID=$PGID \
-e USERNAME=$USERNAME \
-e PASSWORD=$PASSWORD
-v $CACHE_PATH:/cache \
--device=/dev/fuse \
--restart no \
--privileged \
amitie10g/megafuse:latest
```
Note: `--privileged` is not longer required since Linux 4.18. However, I tested in my Ubuntu 19.04 (Linux 5.0), and I got `fusermount: mount failed: Operation not permitted`, so, it should stay enabled.

## Inregrating with your own Alpine-based images
```
FROM <yourimage>

COPY --from=amitie10g/megafuse:binary / /
RUN apk --no-cache add \
      crypto++ \
      libcrypto1.1 \
      libcurl \
      freeimage \
      db-c++ \
      fuse \
      <your packages> && \
    ln -s libcryptopp.so /usr/lib/libcryptopp.so.5.6 && \
    ln -s libmega.so.30503.0.0 /lib/libmega.so && \
    ln -s libmega.so.30503.0.0 /lib/libmega.so.30503
``` 
## Licensing
This source tree has been released to the **Public domain** (Unlicense).
