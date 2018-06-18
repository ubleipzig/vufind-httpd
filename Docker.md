# vufind-httpd

*vufind-httpd* is [VuFind]s default webserver, which delivers file-requests and passes requests through to the php-service. the image is based on [httpd:alpine].

The images are extended by a default configuration with [VuFind]-specific configuration. The configuration expects the [VuFind]-Sources under `/usr/local/vufind`, and the cache-files which are built at [VuFind]-runtime under `/var/cache/vufind`.

Also i created a new *entrypoint* that modifies the configuration based on the value from the environment variable `BASE_PATH`. If you want to access [VuFind] unter http://localhost/vufind you need to provide the environment variable `BASE_PATH=/vufind` on container-create.

## supported tags

* 2.4-*, 2.4, 2, latest ([2.4/Dockerfile])
* vufind1-2.4-*, vufind1-2.4, vufind1-2, vufind1 ([vufind1/Dockerfile])

## Usage of the image

Usage makes only sense in connection with with [vufind-php], which provides the application server of [VuFind]. The server needs to be available as host *php*. Also the [VuFind]-files need to be connected to the container, so that the webserver can serve static content. [VuFind] creates additional cache-files, which have to be served by the webserver. This folder needs to be connected to the container as well.

You can start the webserver as follows:n:
```
#$ docker run --name httpd \
  --link php:php \
  --volume /path/to/vufind:/usr/local/vufind:ro \
  --volume /path/to/cache:/var/cache/vufind:ro \
  --environment BASE_PATH=/vufind
  ubleipzig/vufind-httpd
```
## advanced configuration

### SSL

To start containers with ssl-support enabled one has to modify the containers startup command:

    httpd -D FOREGROUND -D ssl

By this apache will load all necessary modules and configuration to enable SSL on port 443. The provided key and certificate are self-signed and not meant for production usage. to provide a valid key and certificate the files `/usr/local/apache2/conf/server.key` and `/usr/local/apache2/conf/server.crt` have to be overridden e.g.

```bash
$# docker run --name httpd \
  --link php:php \
  --volume /path/to/vufind:/usr/local/vufind:ro \
  --volume /path/to/cache:/var/cache/vufind:ro \
  --volume /path/to/ssl_key.pem:/usr/local/apache2/conf/server.key:ro \
  --volume /path/to/ssl_cert.pem:/usr/local/apache2/conf/server.crt \
  --environment BASE_PATH=/vufind \
  ubleipzig/vufind-httpd \
  httpd -D FOREGROUND -D ssl
```

## Notes

* The _vufind1-*_-Image exists only for development purposes. It enables developers to easily switch containers when they have VuFind1-work to do. The `BASE_PATH`-Option will not work for this Images
* There are no tests until i know how to write them for Docker-images

[VuFind]: https://github.com/vufind-org/vufind
[httpd:alpine]: https://hub.docker.com/_/httpd/
[2.4/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/2.4/Dockerfile
[vufind1/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/vufind1/Dockerfile
[vufind-php]: https://hub.docker.com/r/ubleipzig/vufind-php/
