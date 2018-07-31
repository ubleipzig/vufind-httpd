# vufind-httpd

*vufind-httpd* is [VuFind]s default webserver, which delivers file-requests and passes requests through to the php-service. The image is based on [ubleipzig/httpd].

The images are extended by a default configuration with [VuFind]-specific configuration. The configuration expects the [VuFind]-Sources under `/usr/local/vufind`, and the cache-files which are built at [VuFind]-runtime under `/var/cache/vufind`.

If you want to access [VuFind] unter http://localhost/vufind you need to provide the environment variable `BASE_PATH=/vufind` on container-create.

## supported tags

* 2.4-*, 2.4, 2, latest ([2.4/Dockerfile])
* vufind1-2.4-*, vufind1-2.4, vufind1-2, vufind1 ([vufind1/Dockerfile])

## Usage of the image

Usage makes only sense in connection with with [vufind-php], which provides the application server of [VuFind]. The server needs to be available as host *php*. Also the [VuFind]-files need to be connected to the container, so that the webserver can serve static content. [VuFind] creates additional cache-files, which have to be served by the webserver. This folder needs to be connected to the container as well.

You can start the webserver as follows:
```
#$ docker run --name httpd \
  --link php:php \
  --volume /path/to/vufind:/usr/local/vufind:ro \
  --volume /path/to/cache:/var/cache/vufind:ro \
  --environment BASE_PATH=/vufind
  ubleipzig/vufind-httpd
```

## advanced configuration

* `BASE_PATH=""`: the base-path under which VuFind is reachable
* `FCGI_TARGET=php:9000`: the FCGI target where the application service is accessible

## Notes

* The _vufind1-*_-Image exists only for development purposes. It enables developers to easily switch containers when they have VuFind1-work to do. The options from *advanced configuration* will not work for this Images
* There are no tests until i know how to write them for Docker-images

[VuFind]: https://github.com/vufind-org/vufind
[ubleipzig/httpd]: https://hub.docker.com/_/httpd/
[2.4/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/2.4/Dockerfile
[vufind1/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/vufind1/Dockerfile
[vufind-php]: https://hub.docker.com/r/ubleipzig/vufind-php/
