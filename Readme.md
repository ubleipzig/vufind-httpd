# vufind-httpd

*vufind-httpd* is vufinds default webserver, which delivers file-requests and passes requests through to the php-service. the image is based on [httpd:alpine].

The images is extended by a default configuration with VuFind-specific configuration. The configuration expects the VuFind-Sources under `/usr/local/vufind`, and the cache-files which are built at VuFind-runtime under `/var/cache/vufind`.

Also i created a new *entrypoint* that modifies the configuration based on the value from the environment variable `BASE_PATH`. If you want to access VuFind unter http://localhost/vufind you need to provide the environment variable `BASE_PATH=/vufind` on container-create.

## supported tags

* 2.4-*, 2.4, 2, latest ([2.4/Dockerfile])
* 2.4-*-vufind, 2.4-vufind1, 2-vufind1, vufind1 ([vufind1/Dockerfile])

## Usage of the image

Usage makes only sense in connection with with [vufind-php], which provides the application server of VuFind. The server needs to be available as host *php*. Also the  VuFind-files need to be connected to the container, so that the webserver can serve static content. VuFind creates additional cache-files, which have to be served by the webserver. This folder needs to be connected to the container as well.

You can start the webserver as follows:n:
```
#$ docker run --name httpd \
  --link php:php \
  --volume /path/to/vufind:/usr/local/vufind:ro \
  --volume /path/to/cache:/var/cache/vufind:ro \
  --environment BASE_PATH=/vufind
  ubleipzig/vufind-httpd
```

## Notes

* The _*-vufind1_-Image exists only for development purposes. It enables developers to easily switch containers when they have VuFind1-work to do. The `BASE_PATH`-Option will not work for this Images
* there are no tests until i know how to write them for Docker-images

[httpd:alpine]: https://hub.docker.com/_/httpd/
[2.4/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/2.4/Dockerfile
[vufind1/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/vufind1/Dockerfile
[vufind-php]: https://hub.docker.com/r/ubleipzig/vufind-php/
